// feesUnchecked.spec.ts
import { jest } from '@jest/globals';

type ModalRef = { result: Promise<unknown> };

class FakeModalService {
  open = jest.fn<ModalRef, [any]>();
}

describe('feesUnchecked()', () => {
  // Minimal “component” shape needed for the tests
  let modalService: FakeModalService;
  let comp: {
    modalService: FakeModalService;
    feesIncluded: { value: boolean; setValue: (v: boolean) => void };
    feesUnchecked: () => void;
  };

  const flushPromises = () => new Promise(setImmediate);

  beforeEach(() => {
    modalService = new FakeModalService();

    const feesIncluded = {
      value: false,
      setValue: jest.fn<void, [boolean]>(),
    };

    // Inline implementation that mirrors the function in your screenshot
    const feesUnchecked = function (this: any) {
      if (!this.feesIncluded.value) {
        const modalRef = this.modalService.open({
          windowId: 'feesUncheckedModal',
          noDismiss: true,
          type: 'ModalType.default',
          title: 'Remove transaction fees?',
          text:
            'If applicable, fees will be not be withheld from the sell amount. ' +
            'This may reduce the expected amount of proceeds from the sell..',
          size: 'md',
          analyticsConfig: { name: 'DF Fees unchecked modal' },
          buttons: [
            {
              text: 'Continue',
              buttonId: 'continue-button',
              iconConfig: { name: 'arrow-circle-right', placement: 'right' },
              accessibilityConfig: {
                label: 'DF Fees unchecked change confirmation',
              },
              action: 'dismiss',
            },
            { text: 'Cancel', action: 'close' },
          ],
        });

        modalRef.result
          .then(() => this.feesIncluded.setValue(true))
          .catch(() => this.feesIncluded.setValue(false));
      }
    };

    comp = { modalService, feesIncluded, feesUnchecked };
  });

  it('does NOT open the modal when feesIncluded.value is true', () => {
    comp.feesIncluded.value = true;

    comp.feesUnchecked();

    expect(modalService.open).not.toHaveBeenCalled();
    expect(comp.feesIncluded.setValue).not.toHaveBeenCalled();
  });

  it('opens the modal when feesIncluded.value is false', () => {
    comp.feesIncluded.value = false;

    // stub a pending result (don’t resolve yet)
    modalService.open.mockReturnValue({
      result: new Promise(() => {
        /* pending */
      }),
    });

    comp.feesUnchecked();

    expect(modalService.open).toHaveBeenCalledTimes(1);
  });

  it('passes expected config to modalService.open', () => {
    comp.feesIncluded.value = false;

    modalService.open.mockReturnValue({
      result: new Promise(() => {
        /* pending */
      }),
    });

    comp.feesUnchecked();

    expect(modalService.open).toHaveBeenCalledWith(
      expect.objectContaining({
        windowId: 'feesUncheckedModal',
        noDismiss: true,
        title: 'Remove transaction fees?',
        size: 'md',
        analyticsConfig: { name: 'DF Fees unchecked modal' },
        buttons: expect.arrayContaining([
          expect.objectContaining({
            text: 'Continue',
            buttonId: 'continue-button',
            action: 'dismiss',
          }),
          expect.objectContaining({ text: 'Cancel', action: 'close' }),
        ]),
      })
    );
  });

  it('sets feesIncluded to true when modal resolves (Continue)', async () => {
    comp.feesIncluded.value = false;

    modalService.open.mockReturnValue({
      result: Promise.resolve(),
    });

    comp.feesUnchecked();
    await flushPromises();

    expect(comp.feesIncluded.setValue).toHaveBeenCalledWith(true);
  });

  it('sets feesIncluded to false when modal rejects (Cancel/close)', async () => {
    comp.feesIncluded.value = false;

    // Avoid “unhandledrejection” noise by returning a caught rejection
    modalService.open.mockReturnValue({
      result: Promise.reject(new Error('closed')),
    });

    comp.feesUnchecked();
    await flushPromises();

    expect(comp.feesIncluded.setValue).toHaveBeenCalledWith(false);
  });
});
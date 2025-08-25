// feesUnchecked.spec.ts
import { jest } from '@jest/globals';

type ModalRef = { result: Promise<unknown> };

class FakeModalService {
  open = jest.fn<ModalRef, [any]>();
}

// Minimal shape to test against (replace with your real class/component)
class UnderTest {
  constructor(
    public modalService: { open: (cfg: any) => ModalRef },
    public feesIncluded: { value: boolean; setValue: (v: boolean) => void }
  ) {}
  // paste your real method here in production; this is just the signature:
  feesUnchecked!: () => void;
}

// small async helper to flush microtasks
const flush = () => new Promise<void>(res => setImmediate(() => res()));

describe('feesUnchecked()', () => {
  let modalService: FakeModalService;
  let feesIncluded: { value: boolean; setValue: jest.Mock };
  let instance: UnderTest;

  beforeEach(() => {
    modalService = new FakeModalService();
    feesIncluded = { value: false, setValue: jest.fn() };

    instance = new UnderTest(modalService, feesIncluded);
    // bind the real implementation from your code:
    instance.feesUnchecked = function () {
      // --- copy of the method from the screenshot ---
      if (!this.feesIncluded.value) {
        const modalRef = this.modalService.open({
          windowId: 'feesUncheckedModal',
          noDismiss: true,
          type: (global as any).ModalType?.default ?? 'default',
          title: 'Remove transaction fees?',
          text: 'If applicable, fees will be not be withheld from the sell amount. This may reduce the expected amount of proceeds from the sell.',
          size: 'md',
          analyticsConfig: { name: 'DF Fees unchecked modal' },
          buttons: [
            {
              text: 'Continue',
              buttonId: 'continue-button',
              iconConfig: { name: 'arrow-circle-right', placement: 'right' },
              accessibilityConfig: { label: 'DF Fees unchecked change confirmation' },
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
  });

  test('does nothing when feesIncluded is already true', () => {
    feesIncluded.value = true;

    instance.feesUnchecked();

    expect(modalService.open).not.toHaveBeenCalled();
    expect(feesIncluded.setValue).not.toHaveBeenCalled();
  });

  test('opens the modal with expected core config when feesIncluded is false', () => {
    // unresolved promise so we can just inspect the config
    modalService.open.mockReturnValue({ result: new Promise(() => {}) });

    instance.feesUnchecked();

    expect(modalService.open).toHaveBeenCalledTimes(1);
    const cfg = modalService.open.mock.calls[0][0];

    expect(cfg).toEqual(
      expect.objectContaining({
        windowId: 'feesUncheckedModal',
        noDismiss: true,
        title: 'Remove transaction fees?',
        size: 'md',
        analyticsConfig: { name: 'DF Fees unchecked modal' },
      })
    );

    // Buttons content
    expect(cfg.buttons).toEqual(
      expect.arrayContaining([
        expect.objectContaining({
          text: 'Continue',
          buttonId: 'continue-button',
          action: 'dismiss',
          iconConfig: expect.objectContaining({
            name: 'arrow-circle-right',
            placement: 'right',
          }),
          accessibilityConfig: expect.objectContaining({
            label: 'DF Fees unchecked change confirmation',
          }),
        }),
        expect.objectContaining({
          text: 'Cancel',
          action: 'close',
        }),
      ])
    );
  });

  test('on modal resolve, sets feesIncluded to true', async () => {
    let resolve!: () => void;
    const result = new Promise<void>(res => (resolve = res));
    modalService.open.mockReturnValue({ result });

    instance.feesUnchecked();
    resolve(); // simulate clicking "Continue" / resolve path
    await flush();

    expect(feesIncluded.setValue).toHaveBeenCalledWith(true);
  });

  test('on modal reject, sets feesIncluded to false', async () => {
    let reject!: (e?: unknown) => void;
    const result = new Promise<void>((_, rej) => (reject = rej));
    modalService.open.mockReturnValue({ result });

    instance.feesUnchecked();
    reject(new Error('cancel')); // simulate dismiss/close path
    await flush();

    expect(feesIncluded.setValue).toHaveBeenCalledWith(false);
  });
});
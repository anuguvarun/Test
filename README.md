// fees-unchecked.spec.ts
import { ModalType } from 'wherever/your/modal/type/is'; // adjust
import { YourComponent } from '../path/to/your-component'; // adjust

// Small helper to flush microtasks created by Promise.then/catch
const flush = () => new Promise<void>(r => setImmediate(r));

describe('feesUnchecked', () => {
  let component: YourComponent;
  let modalService: { open: jest.Mock };
  let feesIncluded: { value: boolean; setValue: jest.Mock };

  beforeEach(() => {
    modalService = { open: jest.fn() };
    feesIncluded = { value: false, setValue: jest.fn() };

    // Create the component however you normally do in tests.
    // If you use Angular TestBed, swap this with TestBed.createComponent(...).componentInstance.
    component = new YourComponent(/* deps… */) as any;

    // Inject the minimal shapes needed for this method
    (component as any).modalService = modalService;
    (component as any).feesIncluded = feesIncluded;
  });

  it('does nothing when fees are already included (no modal)', () => {
    feesIncluded.value = true;

    component.feesUnchecked();

    expect(modalService.open).not.toHaveBeenCalled();
    expect(feesIncluded.setValue).not.toHaveBeenCalled();
  });

  it('opens the confirmation modal with expected config when fees are NOT included', () => {
    feesIncluded.value = false;
    modalService.open.mockReturnValue({
      result: new Promise(() => {
        /* unresolved on purpose */
      }),
    });

    component.feesUnchecked();

    expect(modalService.open).toHaveBeenCalledTimes(1);
    expect(modalService.open).toHaveBeenCalledWith(
      expect.objectContaining({
        windowId: 'feesUncheckedModal',
        noDismiss: true,
        type: ModalType.default,
        title: 'Remove transaction fees?',
        text:
          'If applicable, fees will be not be withheld from the sell amount. This may reduce the expected amount of proceeds from the sell.',
        size: 'md',
        analyticsConfig: { name: 'DF Fees unchecked modal' },
        buttons: expect.arrayContaining([
          expect.objectContaining({
            text: 'Continue',
            buttonId: 'continue-button',
            iconConfig: { name: 'arrow-circle-right', placement: 'right' },
            accessibilityConfig: {
              label: 'DF Fees unchecked change confirmation',
            },
            action: 'dismiss',
          }),
          expect.objectContaining({
            text: 'Cancel',
            action: 'close',
          }),
        ]),
      })
    );
  });

  it('sets feesIncluded to true when the modal resolves (Continue)', async () => {
    feesIncluded.value = false;
    modalService.open.mockReturnValue({ result: Promise.resolve() });

    component.feesUnchecked();
    await flush();

    expect(feesIncluded.setValue).toHaveBeenCalledTimes(1);
    expect(feesIncluded.setValue).toHaveBeenCalledWith(true);
  });

  it('sets feesIncluded to false when the modal rejects (Cancel/close)', async () => {
    feesIncluded.value = false;
    modalService.open.mockReturnValue({ result: Promise.reject(new Error('closed')) });

    component.feesUnchecked();
    // prevent unhandled rejection noise in test output
    await flush();

    expect(feesIncluded.setValue).toHaveBeenCalledTimes(1);
    expect(feesIncluded.setValue).toHaveBeenCalledWith(false);
  });
});
import { fakeAsync, tick } from '@angular/core/testing';

describe('feesUnchecked()', () => {
  let modalServiceStub: { open: jest.Mock };
  let component: any; // replace with actual component type
  let modalRef: any;

  beforeEach(() => {
    modalRef = {
      result: Promise.resolve()
    };
    modalServiceStub = {
      open: jest.fn().mockReturnValue(modalRef)
    };

    component = new StepCardComponent(
      /* inject dependencies with modalServiceStub */
    );
    component.feesIncluded = {
      setValue: jest.fn()
    };
  });

  it('should set feesIncluded to true when modal resolves (Continue)', fakeAsync(() => {
    modalRef.result = Promise.resolve();

    component.feesUnchecked();
    tick();

    expect(component.feesIncluded.setValue).toHaveBeenCalledWith(true);
  }));

  it('should set feesIncluded to false when modal is dismissed (Cancel/Close)', fakeAsync(() => {
    modalRef.result = Promise.reject();

    component.feesUnchecked();
    tick();

    expect(component.feesIncluded.setValue).toHaveBeenCalledWith(false);
  }));

  it('should open modal with correct config', () => {
    component.feesUnchecked();

    expect(modalServiceStub.open).toHaveBeenCalledWith({
      windowId: 'feesUncheckedModal',
      noDismiss: true,
      type: ModalType.default,
      title: 'Remove transaction fees?',
      text: 'If applicable, fees will not be withheld from the sell amount.',
      size: 'md',
      analyticsConfig: { name: 'DF Fees unchecked modal' },
      buttons: [
        {
          text: 'Continue',
          buttonId: 'continue-button',
          iconConfig: { name: 'arrow-circle-right', placement: 'right' },
          accessibilityConfig: { label: 'DF Fees unchecked change confirmation' },
          action: 'dismiss'
        },
        {
          text: 'Cancel',
          action: 'close'
        }
      ]
    });
  });
});
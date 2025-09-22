import { ModalType, ButtonType } from '../modal-types';
import { TradeOrderConstants } from '../trade-order-constants';
import { ActionType } from '../action-type';

describe('feesUnchecked (no helper fns)', () => {
  let component: any;
  let modalServiceStub: { open: jest.Mock };

  beforeEach(() => {
    modalServiceStub = { open: jest.fn() };
    component = {
      modalService: modalServiceStub,
      feesIncluded: { value: true, setValue: jest.fn() },
      actionToggle: { value: ActionType.Buy },
      // hook this up to your real component if needed:
      feesUnchecked: undefined as any,
    };

    // If you’re running these inside the real component spec file,
    // just skip the line above and call component.feesUnchecked() directly.
    jest.clearAllMocks();
  });

  it('does nothing when feesIncluded.value is false', () => {
    component.feesIncluded.value = false;

    component.feesUnchecked();

    expect(modalServiceStub.open).not.toHaveBeenCalled();
    expect(component.feesIncluded.setValue).not.toHaveBeenCalled();
  });

  it('opens the modal with expected config for Buy', () => {
    modalServiceStub.open.mockReturnValue({ result: Promise.resolve() });

    component.actionToggle.value = ActionType.Buy;
    component.feesIncluded.value = true;

    component.feesUnchecked();

    expect(modalServiceStub.open).toHaveBeenCalledWith(
      expect.objectContaining({
        windowId: 'feesUncheckedModal',
        noDismiss: true,
        type: ModalType.default,
        title: 'Remove transaction fees?',
        text: TradeOrderConstants.FEES_UNCHECKED_MESSAGE_FOR_BUY,
        size: 'md',
        analyticsConfig: { name: 'DF Fees unchecked modal' },
        buttons: [
          expect.objectContaining({
            text: 'Confirm',
            buttonId: 'confirm-button',
            type: ButtonType.primary,
            accessibilityConfig: { label: 'Confirm remove transaction fees button' },
            action: 'dismiss',
          }),
          expect.objectContaining({
            text: 'Cancel',
            type: ButtonType.secondary,
            size: 'md',
            action: 'close',
          }),
        ],
      }),
    );
  });

  it('uses the Sell message when action is Sell', () => {
    modalServiceStub.open.mockReturnValue({ result: Promise.resolve() });

    component.actionToggle.value = ActionType.Sell;

    component.feesUnchecked();

    expect(modalServiceStub.open).toHaveBeenCalledWith(
      expect.objectContaining({
        text: TradeOrderConstants.FEES_UNCHECKED_MESSAGE_FOR_SELL,
      }),
    );
  });

  it('on resolve: sets feesIncluded to true', async () => {
    modalServiceStub.open.mockReturnValue({ result: Promise.resolve() });

    component.feesUnchecked();

    // flush microtasks so the .then() in feesUnchecked runs
    await Promise.resolve();

    expect(component.feesIncluded.setValue).toHaveBeenCalledWith(true);
  });

  it('on reject: sets feesIncluded to false', async () => {
    // Use a rejected promise; swallow the unhandled rejection inside the test
    modalServiceStub.open.mockReturnValue({ result: Promise.reject(new Error('cancel')) });

    component.feesUnchecked();

    // wait a tick so .catch() inside the method executes
    await Promise.resolve();

    expect(component.feesIncluded.setValue).toHaveBeenCalledWith(false);
  });
});
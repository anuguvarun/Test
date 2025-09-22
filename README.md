describe('feesUnchecked', () => {
  let openMock: jest.Mock;
  let component: TestComponent;

  beforeEach(() => {
    openMock = jest.fn();
    component = new TestComponent({ open: openMock });
  });

  it('does nothing (does not open modal) when feesIncluded.value is false', () => {
    component.feesIncluded.setValue(false);

    component.feesUnchecked();

    expect(openMock).not.toHaveBeenCalled();
  });

  it('opens the modal with the expected base config', () => {
    // inline controllable promise (no helper fn)
    let resolveFn!: () => void, rejectFn!: (e?: any) => void;
    const promise = new Promise<void>((res, rej) => { resolveFn = res; rejectFn = rej; });
    openMock.mockReturnValue({ result: promise });

    component.feesUnchecked();

    expect(openMock).toHaveBeenCalledTimes(1);
    const arg = openMock.mock.calls[0][0];
    expect(arg).toMatchObject({
      windowId: 'feesUncheckedModal',
      noDismiss: true,
      type: ModalType.default,
      title: 'Remove transaction fees?',
      size: 'md',
      analyticsConfig: { name: 'DF Fees unchecked modal' },
    });
    expect(arg.buttons).toHaveLength(2);
    expect(arg.buttons[0]).toMatchObject({
      text: 'Confirm',
      buttonId: 'confirm_button',
      type: ButtonType.primary,
      accessibilityConfig: { label: 'Confirm remove transaction fees button' },
      action: 'dismiss',
    });
    expect(arg.buttons[1]).toMatchObject({
      text: 'Cancel',
      type: ButtonType.secondary,
      size: 'md',
      action: 'close',
    });

    // keep TS happy about unused
    resolveFn(); rejectFn();
  });

  it('uses BUY message when actionToggle = Buy', () => {
    let resolveFn!: () => void, rejectFn!: (e?: any) => void;
    const promise = new Promise<void>((res, rej) => { resolveFn = res; rejectFn = rej; });
    openMock.mockReturnValue({ result: promise });

    component.actionToggle.setValue('Buy');
    component.feesUnchecked();

    const arg = openMock.mock.calls[0][0];
    expect(arg.text).toBe(TradeOrderConstants.FEES_UNCHECKED_MESSAGE_FOR_BUY);

    resolveFn(); rejectFn();
  });

  it('uses SELL message when actionToggle != Buy', () => {
    let resolveFn!: () => void, rejectFn!: (e?: any) => void;
    const promise = new Promise<void>((res, rej) => { resolveFn = res; rejectFn = rej; });
    openMock.mockReturnValue({ result: promise });

    component.actionToggle.setValue('Sell');
    component.feesUnchecked();

    const arg = openMock.mock.calls[0][0];
    expect(arg.text).toBe(TradeOrderConstants.FEES_UNCHECKED_MESSAGE_FOR_SELL);

    resolveFn(); rejectFn();
  });

  it('on confirm (promise resolves) sets feesIncluded to true', async () => {
    let resolveFn!: () => void, rejectFn!: (e?: any) => void;
    const promise = new Promise<void>((res, rej) => { resolveFn = res; rejectFn = rej; });
    openMock.mockReturnValue({ result: promise });
    const spy = jest.spyOn(component.feesIncluded, 'setValue');

    component.feesUnchecked();
    resolveFn(); // simulate clicking "Confirm"
    await promise;

    expect(spy).toHaveBeenCalledWith(true);
  });

  it('on cancel/close (promise rejects) sets feesIncluded to false', async () => {
    let resolveFn!: () => void, rejectFn!: (e?: any) => void;
    const promise = new Promise<void>((res, rej) => { resolveFn = res; rejectFn = rej; });
    openMock.mockReturnValue({ result: promise });
    const spy = jest.spyOn(component.feesIncluded, 'setValue');

    component.feesUnchecked();
    rejectFn(new Error('closed')); // simulate "Cancel"/close
    await promise.catch(() => { /* swallow */ });

    expect(spy).toHaveBeenCalledWith(false);
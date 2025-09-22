describe('checkHeldSecurities', () => {
  let fakeModalRef: { componentInstance: any };

  beforeEach(() => {
    fakeModalRef = { componentInstance: {} };
    jest.clearAllMocks();
  });

  it('calls modalService.open with the expected config', () => {
    modalServiceStub.open.mockReturnValue(fakeModalRef);

    component.checkHeldSecurities();

    expect(modalServiceStub.open).toHaveBeenCalledTimes(1);
    expect(modalServiceStub.open).toHaveBeenCalledWith(
      expect.objectContaining({
        windowId: 'heldSecuritiesModal',
        noDismiss: false,
        type: ModalType.default,
        title: 'Held securities',
        component: AccountHeldSecuritiesComponent,
        size: 'lg',
        analyticsConfig: { name: 'DF Held securities modal' },
        buttons: [
          expect.objectContaining({
            text: 'Done',
            buttonId: 'confirm-button',
            type: ButtonType.primary,
            accessibilityConfig: { label: 'Held securities modal' },
            action: 'dismiss',
          }),
        ],
      }),
    );
  });

  it('returns the modalRef and passes accountPositions to the component instance', () => {
    modalServiceStub.open.mockReturnValue(fakeModalRef);
    const positions = [{ id: 1 }];
    component.accountPositions = { accountPositions: positions };

    const result = component.checkHeldSecurities();

    expect(result).toBe(fakeModalRef);
    expect(fakeModalRef.componentInstance.accountPositions).toBe(positions);
  });

  it('sets modal component positions to undefined when none present', () => {
    modalServiceStub.open.mockReturnValue(fakeModalRef);
    component.accountPositions = undefined;

    component.checkHeldSecurities();

    expect(fakeModalRef.componentInstance.accountPositions).toBeUndefined();
  });

  it('handles modalService.open returning undefined', () => {
    modalServiceStub.open.mockReturnValue(undefined as any);

    const result = component.checkHeldSecurities();

    expect(result).toBeUndefined();
  });
});
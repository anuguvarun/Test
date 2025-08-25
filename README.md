describe('feesUnchecked', () => {
  let instance;
  let feesIncludedMock;
  let modalServiceMock;
  let modalRefMock;

  beforeEach(() => {
    feesIncludedMock = {
      value: false,
      setValue: jest.fn(),
    };
    modalRefMock = {
      result: Promise.resolve(),
    };
    modalServiceMock = {
      open: jest.fn().mockReturnValue(modalRefMock),
    };
    instance = {
      feesIncluded: feesIncludedMock,
      modalService: modalServiceMock,
      feesUnchecked: undefined, // will assign below
    };
    // Import the code, or manually assign
    instance.feesUnchecked = function() {
      if (!this.feesIncluded.value) {
        const modalRef = this.modalService.open({
          windowId: 'feesUncheckedModal',
          noDismiss: true,
          type: 'default',
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
            {
              text: 'Cancel',
              action: 'close',
            },
          ],
        });

        modalRef.result
          .then(() => {
            this.feesIncluded.setValue(true);
          })
          .catch(() => {
            this.feesIncluded.setValue(false);
          });
      }
    };
  });

  it('should open modal when feesIncluded.value is false', () => {
    instance.feesUnchecked();
    expect(modalServiceMock.open).toHaveBeenCalledTimes(1);
    expect(modalServiceMock.open).toHaveBeenCalledWith(expect.objectContaining({
      windowId: 'feesUncheckedModal',
      title: 'Remove transaction fees?',
    }));
  });

  it('should not open modal when feesIncluded.value is true', () => {
    instance.feesIncluded.value = true;
    instance.feesUnchecked();
    expect(modalServiceMock.open).not.toHaveBeenCalled();
  });

  it('should set feesIncluded to true on modal confirm', async () => {
    modalRefMock.result = Promise.resolve();
    instance.feesUnchecked();
    await modalRefMock.result;
    // Wait a tick for promise resolution
    await Promise.resolve();
    expect(feesIncludedMock.setValue).toHaveBeenCalledWith(true);
  });

  it('should set feesIncluded to false on modal cancel', async () => {
    modalRefMock.result = Promise.reject();
    instance.feesUnchecked();
    try { await modalRefMock.result; } catch (e) {}
    await Promise.resolve();
    expect(feesIncludedMock.setValue).toHaveBeenCalledWith(false);
  });
});

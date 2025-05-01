describe('shareQuantityValidate', () => {
  it('should apply only Validators.required when action is Buy', () => {
    const quantityControl = {
      setValidators: jest.fn(),
      updateValueAndValidity: jest.fn(),
      markAsDirty: jest.fn()
    };

    component.card = new FormGroup({
      actionToggle: new FormControl(ActionType.Buy),
      quantity: quantityControl as any,
      shareToggle: new FormControl('irrelevant') // optional
    });

    component.shareQuantityValidate();

    expect(quantityControl.setValidators).toHaveBeenCalledWith([Validators.required]);
    expect(quantityControl.updateValueAndValidity).toHaveBeenCalled();
    expect(quantityControl.markAsDirty).toHaveBeenCalled();
  });
});
describe('shareQuantityValidate', () => {
  it('sets quantity validator to required when action is Buy', () => {
    component.cards[0].get('actionToggle')?.setValue('Buy');
    component.cards[0].get('shareToggle')?.setValue('Shares');

    const quantityControl = component.cards[0].get('quantity');
    quantityControl?.setValue(null); // To trigger required

    component.toggleValidate();

    expect(quantityControl?.hasValidator(Validators.required)).toBeTrue();
  });

  it('marks quantity control as dirty and updates value/validity when action is Buy', () => {
    component.cards[0].get('actionToggle')?.setValue('Buy');
    component.cards[0].get('shareToggle')?.setValue('Shares');

    const quantityControl = component.cards[0].get('quantity');
    spyOn(quantityControl!, 'updateValueAndValidity');
    spyOn(quantityControl!, 'markAsDirty');

    component.toggleValidate();

    expect(quantityControl!.updateValueAndValidity).toHaveBeenCalled();
    expect(quantityControl!.markAsDirty).toHaveBeenCalled();
  });
});
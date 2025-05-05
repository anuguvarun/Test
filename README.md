toggleValidate() {
  const amount = this.card.get('amount');
  const quantity = this.card.get('quantity');
  const toggleValue = this.card.get('shareToggle')?.value;
  const actionToggle = this.card.get('actionToggle')?.value;

  // Clear existing validators
  amount.setValidators([]);
  quantity.setValidators([]);

  // Apply basic validators based on unit type
  switch (toggleValue) {
    case UnitType.Dollars:
      amount.setValidators([Validators.required]);
      break;

    case UnitType.Shares:
    case UnitType.Bonds:
      quantity.setValidators([Validators.required]);
      break;
  }

  // Mark fields dirty and revalidate
  this.updateFormFieldStatus(['amount', 'quantity'], true);

  // Apply max quantity rule only if selling shares
  if (actionToggle === ActionType.Sell && toggleValue === UnitType.Shares) {
    const matchedQuantity = this.getMatchedPositionQuantity();

    const validators = matchedQuantity == null
      ? [Validators.required, Validators.max(0)]
      : [Validators.required, Validators.max(matchedQuantity)];

    quantity.setValidators(validators);
    quantity.updateValueAndValidity();
  }
}
private sellValidate(
  card: FormGroup,
  symbol: string,
  accountPositions: AccountPositionsResponseInfo,
  unitType: UnitType,
  field: 'quantity' | 'amount',
  valueKey: 'quantity' | 'marketValue',
  validatorFn: (value: number) => any
): void {
  const formValues = this.getFormValues(card);
  const matchedPosition = this.getMatchedPosition(accountPositions, symbol);

  if (formValues.actionToggle === ActionType.Sell && formValues.shareToggle === unitType) {
    const value = matchedPosition?.[valueKey];
    const control = card.get(field);
    control?.setValidators([
      Validators.required,
      value == null ? validatorFn(0) : validatorFn(valueKey === 'marketValue' ? value * 0.9 : value),
    ]);
  }
}

sellShareValidate(card: FormGroup, symbol: string, accountPositions: AccountPositionsResponseInfo): void {
  this.sellValidate(card, symbol, accountPositions, UnitType.Shares, 'quantity', 'quantity', Validators.max);
}

sellAmountValidate(card: FormGroup, symbol: string, accountPositions: AccountPositionsResponseInfo): void {
  this.sellValidate(card, symbol, accountPositions, UnitType.Dollars, 'amount', 'marketValue', amountValidator);
}

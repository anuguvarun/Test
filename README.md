const selectedUnitType = this.card.get('shareToggle')?.value;

this.presenter.sellValidate({
  card: this.card,
  symbol: this.symbol,
  accountPositions: this.accountPositions,
  actionType: ActionType.Sell,
  unitType: selectedUnitType,
  inputField: selectedUnitType === UnitType.Dollars ? 'amount' : 'quantity',
  responseValue: selectedUnitType === UnitType.Dollars ? 'marketValue' : 'quantity',
  validatorFn: selectedUnitType === UnitType.Dollars ? amountValidator : Validators.max,
});
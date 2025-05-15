sellValidate(config: SellContext): void {
  const { card, symbol, accountPositions } = config;
  const shareToggle = card.get('shareToggle')?.value;
  const actionToggle = card.get('actionToggle')?.value;

  const matchedPosition = this.getMatchedPosition(accountPositions, symbol);

  if (actionToggle !== ActionType.Sell || !matchedPosition) return;

  const validations = [
    {
      unitType: UnitType.Dollars,
      inputField: 'amount',
      responseValue: 'marketValue',
      validatorFn: amountValidator
    },
    {
      unitType: UnitType.Shares,
      inputField: 'quantity',
      responseValue: 'quantity',
      validatorFn: Validators.max
    }
  ];

  for (const v of validations) {
    if (shareToggle === v.unitType) {
      const value = matchedPosition[v.responseValue];
      const field = card.get(v.inputField);

      let maxAllowedValue = 0;
      if (value != null) {
        maxAllowedValue = v.responseValue === 'marketValue' ? value * 0.9 : value;
      }

      field?.setValidators([
        Validators.required,
        v.validatorFn(maxAllowedValue)
      ]);
    }
  }
}
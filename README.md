interface SellValidationConfig {
  card: FormGroup;
  symbol: string;
  accountPositions: AccountPositionsResponseInfo;
  actionType: ActionType;
  unitType: UnitType;
  inputField: 'quantity' | 'amount';
  responseValue: 'quantity' | 'marketValue';
  validatorFn: (value: number) => any;
}

sellValidate(config: SellValidationConfig): void {
  const {
    card,
    symbol,
    accountPositions,
    actionType,
    unitType,
    inputField,
    responseValue,
    validatorFn
  } = config;

  const shareToggle = card.get('shareToggle')?.value;
  const actionToggle = card.get('actionToggle')?.value;
  const matchedPosition = this.getMatchedPosition(accountPositions, symbol);

  if (actionToggle === actionType && shareToggle === unitType) {
    const value = matchedPosition?.[responseValue];
    const field = card.get(inputField);

    let computedValue = 0;
    if (value != null) {
      computedValue = responseValue === 'marketValue' ? value * 0.9 : value;
    }

    field?.setValidators([
      Validators.required,
      validatorFn(computedValue),
    ]);
  }
}
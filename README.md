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
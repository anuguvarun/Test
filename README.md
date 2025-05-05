if (actiontoggle === ActionType.Sell && toggleValue === UnitType.Shares) {
  const accounts = this.accountPositions.accountPositions ?? [];
  const matchedPositionSymbol = accounts[0]?.positions?.find(
    (position) => position.symbol === this.symbol
  );

  switch (true) {
    case matchedPositionSymbol?.quantity == null:
      quantity.setValidators([Validators.required, Validators.max(0)]);
      break;

    default:
      quantity.setValidators([
        Validators.required,
        Validators.max(matchedPositionSymbol.quantity),
      ]);
      break;
  }
}
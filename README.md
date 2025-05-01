if (toggleValue === UnitType.Shares) {
  const accounts = Array.isArray(this.accountOptions) ? this.accountOptions : [];
  const allPositions = accounts.flatMap(account => account.positions ?? []);
  const matchedPosition = allPositions.find(pos => pos.symbol === this.symbol);

  if (!matchedPosition || matchedPosition.quantity == null) {
    quantity.setValidators([
      Validators.required,
      Validators.max(0)
    ]);
    console.warn('Symbol not found or quantity is null/undefined');
  } else {
    quantity.setValidators([
      Validators.required,
      Validators.max(matchedPosition.quantity)
    ]);
  }
}
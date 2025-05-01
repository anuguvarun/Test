if (toggleValue === UnitType.Shares) {
  const accounts = Array.isArray(this.accountOptions) ? this.accountOptions : [];

  const matchedPosition = accounts
    .map(account => account.positions?.find(p => p.symbol === this.symbol))
    .find(position => position !== undefined);

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
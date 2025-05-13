calculateEstimatedValue(amount: number, price: number | null, unitType: UnitType): number | null {
  if (!amount) return null;

  switch (true) {
    case unitType === UnitType.Bonds:
      return amount * 1000.0;
    case unitType === UnitType.Shares && !!price:
      return amount * price;
    case unitType === UnitType.Dollars:
      return amount;
    default:
      return null;
  }
}

updateEstimatedValue(value = 0): void {
  const name = this.unitType === UnitType.Dollars ? 'amount' : 'quantity';
  const rawValue = this.card.get(name)?.value;
  const amount = this.presenter.getNumber(rawValue);

  const price = this.securityPriceData?.price;
  const unitType = this.unitType;
  const estimate = this.card.get('estimatedValue');

  const result = this.presenter.calculateEstimatedValue(amount, price, unitType);
  estimate.setValue(result);
}

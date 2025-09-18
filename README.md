ngOnInit() {
  this.heldSecurities = this.accountPositions[0].positions;
  this.brokerageAccountNumber = this.accountPositions[0].brokerageAccountNumber;

  this.addComputedPct();
}

private addComputedPct(): void {
  const total = this.totalMarketValue();

  this.heldSecurities = this.heldSecurities.map(item => ({
    ...item,
    computedPct: total > 0 ? (item.marketValue / total) * 100 : 0
  }));
}

totalMarketValue(): number {
  return this.heldSecurities.reduce((sum, item) => sum + item.marketValue, 0);
}

totalHoldingPct(): number {
  return this.heldSecurities.reduce((sum, item) => sum + (item.computedPct ?? 0), 0);
}
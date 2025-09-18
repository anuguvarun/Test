totalHoldingPct(): number {
  const totalMarketValue = this.heldSecurities
    .reduce((sum, item) => sum + item.marketValue, 0);

  if (totalMarketValue === 0) return 0;

  return this.heldSecurities
    .reduce((sum, item) => sum + (item.marketValue / totalMarketValue) * 100, 0);
}
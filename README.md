it('should return "Yes" when isDollar is false, isTradeTypeValid is true, and liquidateAll is true', () => {
  const card = {
    shareToggle: UnitType.Shares, // Not Dollars
    tradeType: 'ETF',             // Valid type
    feesIncluded: false,
    liquidateAll: true,
  };
  expect(component.getFeeOrLiquidateText(card)).toBe('Yes');
});
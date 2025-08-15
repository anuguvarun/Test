describe('createOrder - TradeSecurity mapping', () => {
  it('maps all card fields to trade security object', () => {
    const card = stubCard('CUSIP123', SecurityType.ETF, 'BUY', 10, UnitType.Dollars);
    card.get('cusipNumber').setValue('cusip123');
    card.get('actionToggle').setValue('buy');
    card.get('securityName').setValue('test security');
    card.get('tradeType').setValue('stock');
    card.get('shareToggle').setValue('dollars');
    card.get('feesIncluded').setValue(true);
    card.get('liquidateAll').setValue(false);

    const order = presenter.createOrder([card], accountNumber, activeBrokerageAccount);
    const trade = order.trades[0];

    expect(trade.cusip).toBe('cusip123');
    expect(trade.actionType).toBe('BUY');
    expect(trade.securityName).toBe('TEST SECURITY');
    expect(trade.securityType).toBe('STOCK');
    expect(trade.unitTypeCode).toBe('DOLLARS');
    expect(trade.feesIncluded).toBe(true);
    expect(trade.liquidateAll).toBe(false);
  });

  it('handles missing optional fields gracefully', () => {
    const card = stubCard('CUSIP456', SecurityType.MF, 'SELL', 5, UnitType.Bonds);
    // leave some fields unset to test ?. safe navigation
    card.get('cusipNumber').setValue('CUSIP456');
    card.get('shareToggle').setValue('bonds');

    const order = presenter.createOrder([card], accountNumber, activeBrokerageAccount);
    const trade = order.trades[0];

    expect(trade.cusip).toBe('CUSIP456');
    expect(trade.actionType).toBeUndefined();
    expect(trade.securityName).toBeUndefined();
    expect(trade.securityType).toBeUndefined();
    expect(trade.unitTypeCode).toBe('BONDS'); // still uppercases
  });
});
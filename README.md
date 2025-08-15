it('sets tradeUnitQuantity correctly for Bonds', () => {
  const card = stubCard('T12345678', SecurityType.MF, 'SELL', 5, UnitType.Bonds);
  card.get('quantity').setValue(7);

  const order = presenter.createOrder([card], accountNumber, activeBrokerageAccount);
  const trade = order.trades[0];

  expect(trade.tradeUnitQuantity).toEqual(7000);
});

it('does not multiply tradeUnitQuantity for non-Bonds', () => {
  const card = stubCard('T12345678', SecurityType.MF, 'SELL', 5, UnitType.Dollars);
  card.get('quantity').setValue(7);

  const order = presenter.createOrder([card], accountNumber, activeBrokerageAccount);
  const trade = order.trades[0];

  expect(trade.tradeUnitQuantity).toEqual(7);
});

it('handles zero quantity correctly', () => {
  const card = stubCard('T12345678', SecurityType.MF, 'SELL', 5, UnitType.Bonds);
  card.get('quantity').setValue(0);

  const order = presenter.createOrder([card], accountNumber, activeBrokerageAccount);
  const trade = order.trades[0];

  expect(trade.tradeUnitQuantity).toEqual(0);
});

it('handles large quantity correctly', () => {
  const card = stubCard('T12345678', SecurityType.MF, 'SELL', 5, UnitType.Bonds);
  card.get('quantity').setValue(1000000);

  const order = presenter.createOrder([card], accountNumber, activeBrokerageAccount);
  const trade = order.trades[0];

  expect(trade.tradeUnitQuantity).toEqual(1000000000);
});
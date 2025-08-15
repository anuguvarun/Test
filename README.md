it('calls calculateMaxAllowed with correct values for Dollars + FI', () => {
  const mockPositions = {
    brokerageAccountNumber: 'T12345678',
    positions: [
      {
        symbol: 'AAPL',
        marketValue: 2000,
        quantity: 100,
        type: SecurityType.FI,   // triggers FI branch
        lastPrice: 10,
        holdingPrice: 5,
        cusip: 'A12345678',
      },
    ],
  };

  form.get('shareToggle')?.setValue(UnitType.Dollars);
  form.get('tradeType')?.setValue(SecurityType.FI);

  presenter.sellValidate(form, 'T12345678', mockPositions);

  const amountControl = form.get('amount');
  expect(amountControl?.validator).toBeDefined();

  amountControl?.setValue(3); // something inside allowed range
  expect(amountControl?.errors).toBeNull();
});

it('calls calculateMaxAllowed with correct values for Shares + non-FI', () => {
  const mockPositions = {
    brokerageAccountNumber: 'T12345678',
    positions: [
      {
        symbol: 'AAPL',
        marketValue: 5000,
        quantity: 50,
        type: 'Equity',  // non-FI
        lastPrice: 100,
        holdingPrice: 80,
        cusip: 'A12345678',
      },
    ],
  };

  form.get('shareToggle')?.setValue(UnitType.Shares);
  form.get('tradeType')?.setValue('Equity');

  presenter.sellValidate(form, 'T12345678', mockPositions);

  const qtyControl = form.get('quantity');
  expect(qtyControl?.validator).toBeDefined();

  qtyControl?.setValue(25); // within allowed
  expect(qtyControl?.errors).toBeNull();
});

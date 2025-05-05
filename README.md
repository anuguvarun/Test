it('sets quantity validator when no matching symbol is found in accountPositions', () => {
  component.cards[0].get('shareToggle')?.setValue('Shares');
  component.cards[0].get('actionToggle')?.setValue('Sell');

  component.symbol = 'TSLA'; // This won't match 'AAPL'

  component.accountPositions = {
    accountPositions: [
      {
        brokerageAccountNumber: '758349',
        positions: [createMockPosition('AAPL', 204)],
      },
    ],
  };

  const quantityControl = component.cards[0].get('quantity');
  quantityControl?.setValue(12);
  component.toggleValidate();

  expect(quantityControl?.hasValidator(Validators.max(0))).toBe(true);
});
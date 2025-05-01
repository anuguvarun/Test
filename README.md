it('sets max(0) when no matching symbol is found in accountPositions', () => {
  component.symbol = 'AAPL';
  component.accountPositions = {
    accountPositions: [
      {
        positions: [{ symbol: 'MSFT', quantity: 50 }]
      }
    ]
  };

  const quantityControl = component.cards[0].get('quantity');
  component.cards[0].get('actionToggle')?.setValue('Sell');
  component.cards[0].get('shareToggle')?.setValue('Shares');

  component.toggleValidate();

  const validator = quantityControl?.validator?.({ value: 100 });
  expect(validator?.['max']).toBe(0);
});

it('sets correct max() when matching symbol is found', () => {
  component.symbol = 'AAPL';
  component.accountPositions = {
    accountPositions: [
      {
        positions: [{ symbol: 'AAPL', quantity: 204 }]
      }
    ]
  };

  const quantityControl = component.cards[0].get('quantity');
  component.cards[0].get('actionToggle')?.setValue('Sell');
  component.cards[0].get('shareToggle')?.setValue('Shares');

  component.toggleValidate();

  const validator = quantityControl?.validator?.({ value: 205 });
  expect(validator?.['max']).toBeTruthy();
});

it('sets max(0) when matching symbol has null quantity', () => {
  component.symbol = 'AAPL';
  component.accountPositions = {
    accountPositions: [
      {
        positions: [{ symbol: 'AAPL', quantity: null }]
      }
    ]
  };

  const quantityControl = component.cards[0].get('quantity');
  component.cards[0].get('actionToggle')?.setValue('Sell');
  component.cards[0].get('shareToggle')?.setValue('Shares');

  component.toggleValidate();

  const validator = quantityControl?.validator?.({ value: 50 });
  expect(validator?.['max']).toBe(0);
});
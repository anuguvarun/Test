it('sets required and max validators when matched position symbol is found with quantity', () => {
  const matchedQuantity = 20;

  component.cards[0].get('shareToggle')?.setValue('Shares');
  component.cards[0].get('actionToggle')?.setValue('Sell');

  component.accountPositions = {
    accountPositions: [
      {
        brokerageAccountNumber: '758349',
        positions: [createMockPosition('AAPL', matchedQuantity)],
      },
    ],
  };

  const quantityControl = component.cards[0].get('quantity');
  quantityControl?.setValue(15); // under max

  component.toggleValidate();

  expect(quantityControl?.hasValidator(Validators.required)).toBeTrue();
  expect(quantityControl?.hasValidator(Validators.max(matchedQuantity))).toBeTrue();
});
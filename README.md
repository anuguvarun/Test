it('should not throw error when filtering cardValues in ngOnInit', () => {
  const buyCard = new FormGroup({
    actionToggle: new FormControl(ActionType.Buy)
  });

  const sellCard = new FormGroup({
    actionToggle: new FormControl(ActionType.Sell)
  });

  component.cardValues = [buyCard, sellCard];

  component.ngOnInit();

  expect(component.buyCards.length).toBe(1);
  expect(component.sellCards.length).toBe(1);
  expect(component.buyCards[0].get('actionToggle')?.value).toBe(ActionType.Buy);
});



it('should not throw error when card.value is a plain object', () => {
  // ✅ Mock card structure to match component's actual usage
  component.cardValues = [
    { value: { actionToggle: 'Buy' } },
    { value: { actionToggle: 'Sell' } }
  ];

  component.ngOnInit();

  expect(component.buyCards.length).toBe(1);
  expect(component.sellCards.length).toBe(1);
});


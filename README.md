it('should process cardValues correctly on ngOnInit', () => {
  // Arrange
  const buyCard = new FormGroup({
    actionToggle: new FormControl(ActionType.Buy)
  });

  const sellCard = new FormGroup({
    actionToggle: new FormControl(ActionType.Sell)
  });

  // Provide actual FormGroup array, as expected by the component
  component.cardValues = [buyCard, sellCard];

  // Act
  component.ngOnInit();

  // Assert
  expect(component.buyCards.length).toBe(1);
  expect(component.sellCards.length).toBe(1);
  expect(component.buyCards[0].get('actionToggle')?.value).toBe(ActionType.Buy);
  expect(mockPresenter.getCardInfoByAction).toHaveBeenCalledWith(component.cardValues);
});
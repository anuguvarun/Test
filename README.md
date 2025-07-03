it('should disable toggles when cusip is null', () => {
  component.actionToggle.setValue(ActionType.Sell);
  const mockMatchedPosition = { ...mockMatchedPositionWithCusip, cusip: null };

  jest.spyOn(component.presenter, 'getMatchedPosition').mockReturnValue(mockMatchedPosition);

  component.updateToggleVisibilityByCusip();

  expect(component.showUnitToggle).toBe(false);
  expect(component.showAmountOrQuantityField).toBe(false);
});
it('should call sellValidate when action is Sell', () => {
  const sellValidateSpy = jest.spyOn(component.presenter, 'sellValidate');
  
  component.action = { value: ActionType.Sell }; // or component.action.setValue(...) if it's a FormControl
  component.ngOnInit(); // or trigger the method that contains the logic

  expect(sellValidateSpy).toHaveBeenCalledWith(
    component.card,
    component.symbol,
    component.accountPositions
  );
});

it('should not call sellValidate when action is not Sell', () => {
  const sellValidateSpy = jest.spyOn(component.presenter, 'sellValidate');
  
  component.action = { value: ActionType.Buy }; // or any non-Sell action
  component.ngOnInit(); // or trigger the method that contains the logic

  expect(sellValidateSpy).not.toHaveBeenCalled();
});
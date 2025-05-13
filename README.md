it('should return validator with 90% of marketValue', () => {
  const form = new FormGroup({ amount: new FormControl() });
  presenter.sellAmountValidate(form, 'AAPL', mockAccountPositions);
  expect(form.get('amount').validator).toBeDefined();
});
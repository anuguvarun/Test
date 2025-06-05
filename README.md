it('should assign filtered cardValues to buyCards and sellCards', () => {
  component.cardValues = [
    { value: { actionToggle: 'Buy' } },
    { value: { actionToggle: 'Sell' } }
  ];

  component.ngOnInit();

  expect(component.buyCards?.length).toBe(1);
  expect(component.sellCards?.length).toBe(1);

  expect(component.buyCards[0].value.actionToggle).toBe('Buy');
  expect(component.sellCards[0].value.actionToggle).toBe('Sell');
});
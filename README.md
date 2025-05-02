it('should initialize steps and set current and enabled when url matches step.url', () => {
  const mockStep = {
    title: 'Step 1',
    url: 'trade-order-entry',
    current: false,
    enabled: false,
  };
  component.steps = [mockStep];

  mockRouter.url = '/trade-order-entry';

  component.ngOnInit();

  expect(component.hideWayfinder).toBe(false); // no 'success' in URL
  expect(component.steps[0].current).toBe(true);
  expect(component.steps[0].enabled).toBe(true);
});
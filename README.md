it('ngOnInit calls addComputedPct()', () => {
  // spy on the private method (safe for tests)
  const spy = jest.spyOn(component as unknown as { addComputedPct: () => void }, 'addComputedPct');

  component.accountPositions = makeAccountPositions([{ marketValue: 123 }]);
  component.ngOnInit();

  expect(spy).toHaveBeenCalledTimes(1);
});
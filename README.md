it('ngOnInit triggers computedPct calculation through addComputedPct()', () => {
  // Arrange: a position with a known value
  const positions = [{ marketValue: 100 }];
  component.accountPositions = makeAccountPositions(positions);

  // Act: call ngOnInit (which should internally call addComputedPct)
  component.ngOnInit();

  // Assert: effect of addComputedPct should be visible on heldSecurities
  expect(component.heldSecurities[0].computedPct).toBe(100);
});
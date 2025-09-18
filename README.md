const initWith = (
    positions: PositionItem[] = [{ marketValue: 1 }],
    acct = 'ABC123'
  ) => {
    component.accountPositions = makeAccountPositions(positions, acct);
    component.ngOnInit(); // <-- ensures line 24 is executed
  };

  beforeEach(() => {
    component = new AccountHeldSecuritiesComponent();
    component.securityPriceData = {} as SecurityPriceData;

    // run once for every test (so line 24 is always covered)
    initWith();
  });

  it('ngOnInit copies inputs and computes computedPct for each position', () => {
    initWith([
      { marketValue: 100 },
      { marketValue: 50 },
      { marketValue: 350 },
    ]);

    expect(component.totalMarketValue()).toBe(500);
    expect(component.heldSecurities[0].computedPct).toBeCloseTo(20);
  });

  it('totalHoldingPct sums to ~100', () => {
    initWith([
      { marketValue: 200 },
      { marketValue: 300 },
      { marketValue: 500 },
    ]);

    expect(component.totalHoldingPct()).toBeCloseTo(100, 8);
  })
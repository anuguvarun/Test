// account-held-securities.component.spec.ts
import { AccountHeldSecuritiesComponent } from './account-held-securities.component';

interface Position {
  marketValue: number;
  holdingPct?: number;       // not used by the component anymore, but kept for compatibility
  computedPct?: number;       // added by addComputedPct()
}
interface AccountPositionsResponseInfo {
  brokerageAccountNumber: string;
  positions: Position[];
}

describe('AccountHeldSecuritiesComponent (updated)', () => {
  let component: AccountHeldSecuritiesComponent;

  const makeAccountPositions = (
    positions: Position[],
    acct = 'ABC123'
  ): AccountPositionsResponseInfo[] => [
    { brokerageAccountNumber: acct, positions },
  ];

  beforeEach(() => {
    component = new AccountHeldSecuritiesComponent();
  });

  it('ngOnInit should copy inputs and compute computedPct for each position', () => {
    const positions: Position[] = [
      { marketValue: 100 },
      { marketValue: 50 },
      { marketValue: 350 },
    ];
    // total = 500 → computedPct: 20, 10, 70
    component.accountPositions = makeAccountPositions(positions) as any;

    component.ngOnInit();

    expect(component.brokerageAccountNumber).toBe('ABC123');
    expect(component.heldSecurities).toHaveLength(3);

    const totals = component.totalMarketValue();
    expect(totals).toBe(500);

    // Check computedPct values (allow float tolerance)
    const cps = component.heldSecurities.map(p => p.computedPct);
    expect(cps[0]).toBeCloseTo(20);
    expect(cps[1]).toBeCloseTo(10);
    expect(cps[2]).toBeCloseTo(70);

    // also ensure we didn’t mutate the original array reference (map returns new array)
    expect(component.heldSecurities).not.toBe(positions);
  });

  it('totalMarketValue() sums marketValue', () => {
    component.accountPositions = makeAccountPositions([
      { marketValue: 10 },
      { marketValue: 90 },
    ]) as any;

    component.ngOnInit();

    expect(component.totalMarketValue()).toBe(100);
  });

  it('totalHoldingPct() sums computedPct (should be ~100 when total > 0)', () => {
    component.accountPositions = makeAccountPositions([
      { marketValue: 200 },
      { marketValue: 300 },
      { marketValue: 500 },
    ]) as any;

    component.ngOnInit();

    // computedPct should add up to 100 (allow small float error)
    expect(component.totalHoldingPct()).toBeCloseTo(100, 8);
  });

  it('handles empty/zero totals: computedPct set to 0 and sums to 0', () => {
    // Case A: empty array
    component.accountPositions = makeAccountPositions([], 'XYZ999') as any;
    component.ngOnInit();
    expect(component.heldSecurities).toEqual([]);
    expect(component.brokerageAccountNumber).toBe('XYZ999');
    expect(component.totalMarketValue()).toBe(0);
    expect(component.totalHoldingPct()).toBe(0);

    // Case B: non-empty but total == 0 (all zeros)
    component = new AccountHeldSecuritiesComponent();
    component.accountPositions = makeAccountPositions(
      [{ marketValue: 0 }, { marketValue: 0 }]
    ) as any;

    component.ngOnInit();

    expect(component.totalMarketValue()).toBe(0);
    // computedPct should have been set to 0 for each item
    expect(component.heldSecurities.map(p => p.computedPct)).toEqual([0, 0]);
    expect(component.totalHoldingPct()).toBe(0);
  });
});
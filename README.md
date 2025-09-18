
// account-held-securities.component.spec.ts
import { AccountHeldSecuritiesComponent } from './account-held-securities.component';
import { AccountPositionsResponseInfo } from '../models/account-positions-response-info.model';
import { SecurityPriceData } from '../models/security-price-data.model';

/**
 * Minimal local shapes the component actually uses.
 * We then cast them to the real app types (no `any`).
 */
interface TestPosition {
  marketValue: number;
  computedPct?: number;
}
interface TestAccountPositionsElement {
  brokerageAccountNumber: string;
  positions: TestPosition[];
}

/**
 * Build a payload compatible with how the component uses the input:
 * this.accountPositions[0].positions / .brokerageAccountNumber
 *
 * We return it as AccountPositionsResponseInfo via a safe `unknown` cast
 * (keeps code `no-any` while allowing us to manufacture the test data).
 */
const makeAccountPositions = (
  positions: TestPosition[],
  acct = 'ABC123'
): AccountPositionsResponseInfo => {
  const arr: TestAccountPositionsElement[] = [
    { brokerageAccountNumber: acct, positions },
  ];
  return arr as unknown as AccountPositionsResponseInfo;
};

describe('AccountHeldSecuritiesComponent', () => {
  let component: AccountHeldSecuritiesComponent;

  beforeEach(() => {
    component = new AccountHeldSecuritiesComponent();
    // If the component doesn’t use securityPriceData in these tests,
    // a minimal stub keeps types happy without `any`.
    component.securityPriceData = {} as SecurityPriceData;
  });

  it('ngOnInit copies inputs and computes computedPct for each position', () => {
    const positions: TestPosition[] = [
      { marketValue: 100 },
      { marketValue: 50 },
      { marketValue: 350 },
    ];
    // total = 500 → computedPct = 20, 10, 70
    component.accountPositions = makeAccountPositions(positions);

    component.ngOnInit();

    expect(component.brokerageAccountNumber).toBe('ABC123');
    expect(component.heldSecurities).toHaveLength(3);

    expect(component.totalMarketValue()).toBe(500);

    const cps = component.heldSecurities.map(p => p.computedPct);
    expect(cps[0]).toBeCloseTo(20);
    expect(cps[1]).toBeCloseTo(10);
    expect(cps[2]).toBeCloseTo(70);

    // ensure map() created a new array (not the same reference)
    expect(component.heldSecurities).not.toBe(positions);
  });

  it('totalMarketValue() sums marketValue', () => {
    component.accountPositions = makeAccountPositions([
      { marketValue: 10 },
      { marketValue: 90 },
    ]);

    component.ngOnInit();

    expect(component.totalMarketValue()).toBe(100);
  });

  it('totalHoldingPct() sums computedPct (≈100 when total > 0)', () => {
    component.accountPositions = makeAccountPositions([
      { marketValue: 200 },
      { marketValue: 300 },
      { marketValue: 500 },
    ]);

    component.ngOnInit();

    // Sum should be ~100; allow for float rounding
    expect(component.totalHoldingPct()).toBeCloseTo(100, 8);
  });

  it('handles empty and zero totals (computedPct -> 0, sums -> 0)', () => {
    // A) empty
    component.accountPositions = makeAccountPositions([], 'XYZ999');
    component.ngOnInit();

    expect(component.heldSecurities).toEqual([]);
    expect(component.brokerageAccountNumber).toBe('XYZ999');
    expect(component.totalMarketValue()).toBe(0);
    expect(component.totalHoldingPct()).toBe(0);

    // B) non-empty but total == 0
    component = new AccountHeldSecuritiesComponent();
    component.securityPriceData = {} as SecurityPriceData;
    component.accountPositions = makeAccountPositions([
      { marketValue: 0 },
      { marketValue: 0 },
    ]);

    component.ngOnInit();

    expect(component.totalMarketValue()).toBe(0);
    expect(component.heldSecurities.map(p => p.computedPct)).toEqual([0, 0]);
    expect(component.totalHoldingPct()).toBe(0);
  });
});
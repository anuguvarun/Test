// account-held-securities.component.spec.ts
import { AccountHeldSecuritiesComponent } from './account-held-securities.component';
import { AccountPositionsResponseInfo } from '../models/account-positions-response-info.model';
import { SecurityPriceData } from '../models/security-price-data.model';

/**
 * Infer the element shape inside AccountPositionsResponseInfo
 * so we can construct valid test data without `any`/`unknown`.
 */
type AccountItem =
  AccountPositionsResponseInfo extends Array<infer T> ? T : never;
type PositionItem =
  AccountItem extends { positions: Array<infer P> } ? P : never;

/**
 * Factory to build the input exactly as the component expects:
 * this.accountPositions[0].positions / .brokerageAccountNumber
 */
const makeAccountPositions = (
  positions: PositionItem[],
  acct = 'ABC123'
): AccountPositionsResponseInfo => {
  // `item` is precisely the array element type inferred above
  const item: AccountItem = {
    brokerageAccountNumber: acct,
    positions,
  };

  // return as the array type (no `any`/`unknown`)
  return [item];
};

describe('AccountHeldSecuritiesComponent', () => {
  let component: AccountHeldSecuritiesComponent;

  beforeEach(() => {
    component = new AccountHeldSecuritiesComponent();
    component.securityPriceData = {} as SecurityPriceData; // safe: real type, minimal stub
  });

  it('ngOnInit copies inputs and computes computedPct for each position', () => {
    const positions: PositionItem[] = [
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

    // ensure map() produced a new array
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

    // allow float rounding
    expect(component.totalHoldingPct()).toBeCloseTo(100, 8);
  });

  it('ngOnInit triggers computedPct calculation via addComputedPct()', () => {
    component.accountPositions = makeAccountPositions([{ marketValue: 100 }]);

    component.ngOnInit();

    // effect of addComputedPct(): computedPct must be present
    expect(component.heldSecurities[0].computedPct).toBe(100);
  });

  it('handles empty and zero totals (computedPct -> 0, sums -> 0)', () => {
    // A) empty positions
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
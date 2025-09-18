// account-held-securities.component.spec.ts
import { AccountHeldSecuritiesComponent } from './account-held-securities.component';
import { AccountPositionsResponseInfo } from '../models/account-positions-response-info.model';
import { SecurityPriceData } from '../models/security-price-data.model';

describe('AccountHeldSecuritiesComponent (updated)', () => {
  let component: AccountHeldSecuritiesComponent;

  const makeAccountPositions = (
    positions: { marketValue: number }[],
    acct = 'ABC123'
  ): AccountPositionsResponseInfo[] => [
    { brokerageAccountNumber: acct, positions },
  ];

  beforeEach(() => {
    component = new AccountHeldSecuritiesComponent();
    // always give it a valid SecurityPriceData (even if empty for now)
    component.securityPriceData = {} as SecurityPriceData;
  });

  it('ngOnInit should copy inputs and compute computedPct for each position', () => {
    const positions = [
      { marketValue: 100 },
      { marketValue: 50 },
      { marketValue: 350 },
    ];
    component.accountPositions = makeAccountPositions(positions);

    component.ngOnInit();

    expect(component.brokerageAccountNumber).toBe('ABC123');
    expect(component.heldSecurities).toHaveLength(3);

    expect(component.totalMarketValue()).toBe(500);

    const cps = component.heldSecurities.map(p => p.computedPct);
    expect(cps[0]).toBeCloseTo(20);
    expect(cps[1]).toBeCloseTo(10);
    expect(cps[2]).toBeCloseTo(70);
  });

  it('totalMarketValue() sums marketValue', () => {
    component.accountPositions = makeAccountPositions([
      { marketValue: 10 },
      { marketValue: 90 },
    ]);

    component.ngOnInit();

    expect(component.totalMarketValue()).toBe(100);
  });

  it('totalHoldingPct() sums computedPct (should be ~100 when total > 0)', () => {
    component.accountPositions = makeAccountPositions([
      { marketValue: 200 },
      { marketValue: 300 },
      { marketValue: 500 },
    ]);

    component.ngOnInit();

    expect(component.totalHoldingPct()).toBeCloseTo(100, 8);
  });

  it('handles empty/zero totals: computedPct set to 0 and sums to 0', () => {
    // Case A: empty positions
    component.accountPositions = makeAccountPositions([], 'XYZ999');
    component.ngOnInit();

    expect(component.heldSecurities).toEqual([]);
    expect(component.brokerageAccountNumber).toBe('XYZ999');
    expect(component.totalMarketValue()).toBe(0);
    expect(component.totalHoldingPct()).toBe(0);

    // Case B: non-empty but total == 0
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
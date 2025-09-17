// account-held-securities.component.spec.ts
import { AccountHeldSecuritiesComponent } from './account-held-securities.component';

type Position = { marketValue: number; holdingPct: number };
type AccountPositionsResponseInfo = {
  brokerageAccountNumber: string;
  positions: Position[];
};

describe('AccountHeldSecuritiesComponent', () => {
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

  it('ngOnInit should copy inputs to fields', () => {
    const positions: Position[] = [
      { marketValue: 100, holdingPct: 0.2 },
      { marketValue: 50, holdingPct: 0.1 },
    ];
    component.accountPositions = makeAccountPositions(positions) as any;

    component.ngOnInit();

    expect(component.heldSecurities).toBe(positions);
    expect(component.brokerageAccountNumber).toBe('ABC123');
  });

  it('totalMarketValue() should sum marketValue', () => {
    component.accountPositions = makeAccountPositions([
      { marketValue: 10, holdingPct: 0.05 },
      { marketValue: 90, holdingPct: 0.45 },
    ]) as any;

    component.ngOnInit();

    expect(component.totalMarketValue()).toBe(100);
  });

  it('totalHoldingPct() should sum holdingPct', () => {
    component.accountPositions = makeAccountPositions([
      { marketValue: 1, holdingPct: 0.2 },
      { marketValue: 2, holdingPct: 0.3 },
      { marketValue: 3, holdingPct: 0.5 },
    ]) as any;

    component.ngOnInit();

    expect(component.totalHoldingPct()).toBeCloseTo(1.0);
  });

  it('handles empty positions (reduces to 0)', () => {
    component.accountPositions = makeAccountPositions([], 'XYZ999') as any;

    component.ngOnInit();

    expect(component.heldSecurities).toEqual([]);
    expect(component.brokerageAccountNumber).toBe('XYZ999');
    expect(component.totalMarketValue()).toBe(0);
    expect(component.totalHoldingPct()).toBe(0);
  });
});
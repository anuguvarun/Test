import { AccountHeldSecuritiesComponent } from './account-held-securities';

describe('AccountHeldSecuritiesComponent.displayQuantity', () => {
  let component: AccountHeldSecuritiesComponent;

  beforeEach(() => {
    component = new AccountHeldSecuritiesComponent();
  });

  it('should return null if position is null', () => {
    expect(component.displayQuantity(null as any)).toBeNull();
  });

  it('should return null if position.quantity is null', () => {
    const pos: any = { type: 'Bond', quantity: null };
    expect(component.displayQuantity(pos)).toBeNull();
  });

  it('should divide quantity by 1000 if type is Bond', () => {
    const pos: any = { type: 'Bond', quantity: 5000 };
    expect(component.displayQuantity(pos)).toBe(5);
  });

  it('should return raw quantity if type is not Bond', () => {
    const pos: any = { type: 'Stock', quantity: 250 };
    expect(component.displayQuantity(pos)).toBe(250);
  });

  it('should work with other security types (e.g. ETF)', () => {
    const pos: any = { type: 'ETF', quantity: 1234 };
    expect(component.displayQuantity(pos)).toBe(1234);
  });

  it('should handle zero quantity correctly for Bond', () => {
    const pos: any = { type: 'Bond', quantity: 0 };
    expect(component.displayQuantity(pos)).toBe(0);
  });

  it('should handle zero quantity correctly for non-Bond', () => {
    const pos: any = { type: 'Stock', quantity: 0 };
    expect(component.displayQuantity(pos)).toBe(0);
  });
});
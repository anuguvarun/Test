import { StepTwoCardComponent } from './step-two-card.component';
import { SecurityType } from '../path-to-enum/security-type.enum';

describe('StepTwoCardComponent', () => {
  let component: StepTwoCardComponent;

  beforeEach(() => {
    component = new StepTwoCardComponent();
  });

  it('should return true when tradeType is not FI', () => {
    component.cards = [
      { value: { tradeType: SecurityType.ETF } },
      { value: { tradeType: SecurityType.MF } },
    ];
    component.index = 0;
    expect(component.isNotFi()).toBe(true);
  });

  it('should return false when tradeType is FI', () => {
    component.cards = [
      { value: { tradeType: SecurityType.FI } }
    ];
    component.index = 0;
    expect(component.isNotFi()).toBe(false);
  });

  it('should return true when value or tradeType is undefined', () => {
    component.cards = [{ value: undefined }];
    component.index = 0;
    expect(component.isNotFi()).toBe(true);
  });

  it('should not throw if cards is empty', () => {
    component.cards = [];
    component.index = 0;
    expect(() => component.isNotFi()).not.toThrow();
  });
});
import { StepCardComponent } from './step-card.component';
import { Validators, FormControl } from '@angular/forms';
import { ActionType, UnitType } from 'path-to-your-enums'; // adjust import

describe('StepCardComponent - toggleValidate', () => {
  let component: StepCardComponent;

  beforeEach(() => {
    component = new StepCardComponent();
    component.quantity = new FormControl();
    component.symbol = 'AAPL';
  });

  it('should set max(0) when no matching position is found', () => {
    component.accountPositions = { accountPositions: [] };
    component.toggleValidate(ActionType.Sell, UnitType.Shares);
    const validator = component.quantity.validator?.({} as any);
    expect(validator?.['max']).toBe(0);
  });

  it('should set max(0) when matched position quantity is null', () => {
    component.accountPositions = {
      accountPositions: [
        { positions: [{ symbol: 'AAPL', quantity: null }] }
      ]
    };
    component.toggleValidate(ActionType.Sell, UnitType.Shares);
    const validator = component.quantity.validator?.({} as any);
    expect(validator?.['max']).toBe(0);
  });

  it('should set correct max validator when matching position is found', () => {
    component.accountPositions = {
      accountPositions: [
        { positions: [{ symbol: 'AAPL', quantity: 204 }] }
      ]
    };
    component.toggleValidate(ActionType.Sell, UnitType.Shares);
    const validator = component.quantity.validator?.({ value: 205 } as any);
    expect(validator?.['max']).toBeTruthy(); // Quantity 205 exceeds max 204
  });
});
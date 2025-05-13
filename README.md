import { FormControl, FormGroup, Validators } from '@angular/forms';
import { StepCardPresenter } from './step-card.presenter';
import { AccountPositionsResponseInfo } from 'your-models-path';
import { ActionType, UnitType } from 'your-enums-path';

describe('StepCardPresenter - sellShareValidate', () => {
  let presenter: StepCardPresenter;
  let form: FormGroup;

  beforeEach(() => {
    presenter = new StepCardPresenter();
    form = new FormGroup({
      quantity: new FormControl(),
      shareToggle: new FormControl(UnitType.Shares),
      actionToggle: new FormControl(ActionType.Sell),
    });
  });

  it('should apply max validator based on matched position quantity', () => {
    const mockData: AccountPositionsResponseInfo = {
      accountPositions: [
        {
          brokerageAccountNumber: '123',
          positions: [
            {
              symbol: 'AAPL',
              quantity: 50,
              marketValue: 1000,
              description: '',
              type: '',
              lastPrice: 0,
              holdingPct: 0
            }
          ]
        }
      ]
    };

    presenter.sellShareValidate(form, 'AAPL', mockData);

    const control = form.get('quantity')!;
    expect(control.validator).toBeDefined();

    const result = control.validator!({ value: 60 } as any); // above max
    expect(result).toEqual({ max: { max: 50, actual: 60 } });

    const valid = control.validator!({ value: 40 } as any); // valid
    expect(valid).toBeNull();
  });

  it('should apply max(0) if matched position quantity is null', () => {
    const mockData: AccountPositionsResponseInfo = {
      accountPositions: [
        {
          brokerageAccountNumber: '123',
          positions: [
            {
              symbol: 'AAPL',
              quantity: null as any,
              marketValue: 1000,
              description: '',
              type: '',
              lastPrice: 0,
              holdingPct: 0
            }
          ]
        }
      ]
    };

    presenter.sellShareValidate(form, 'AAPL', mockData);
    const control = form.get('quantity')!;
    const result = control.validator!({ value: 1 } as any); // anything > 0 fails
    expect(result).toEqual({ max: { max: 0, actual: 1 } });
  });

  it('should skip validators if shareToggle is not UnitType.Shares', () => {
    form.get('shareToggle')?.setValue(UnitType.Dollars);

    const mockData: AccountPositionsResponseInfo = {
      accountPositions: []
    };

    presenter.sellShareValidate(form, 'AAPL', mockData);

    const control = form.get('quantity')!;
    expect(control.validator).toBeNull(); // no validators set
  });
});
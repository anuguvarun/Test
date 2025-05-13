import { FormGroup, FormControl, Validators } from '@angular/forms';
import { StepCardPresenter } from './step-card.presenter';
import { AccountPositionsResponseInfo } from 'your-model-path';
import { UnitType, ActionType } from 'your-enums';
import { amountValidator } from 'your-validator-util';

describe('StepCardPresenter', () => {
  let presenter: StepCardPresenter;
  let form: FormGroup;

  beforeEach(() => {
    presenter = new StepCardPresenter();
    form = new FormGroup({
      amount: new FormControl(''),
      shareToggle: new FormControl(UnitType.Dollars),
      actionToggle: new FormControl(ActionType.Sell)
    });
  });

  it('should apply validator with 90% of market value if matched position is found', () => {
    const mockAccountPositions: AccountPositionsResponseInfo = {
      accountPositions: [
        {
          brokerageAccountNumber: '123456',
          positions: [
            {
              symbol: 'AAPL',
              marketValue: 1000,
              description: '',
              type: '',
              quantity: 0,
              lastPrice: 0,
              holdingPct: 0
            }
          ]
        }
      ]
    };

    presenter.sellAmountValidate(form, 'AAPL', mockAccountPositions);

    const amountControl = form.get('amount');
    expect(amountControl?.validator).toBeDefined();

    const validatorFn = amountControl?.validator!;
    const result = validatorFn({ value: 999 } as any);
    expect(result).toBeNull(); // Means validation passed
  });

  it('should apply validator with 0 if market value is null', () => {
    const mockAccountPositions: AccountPositionsResponseInfo = {
      accountPositions: [
        {
          brokerageAccountNumber: '123456',
          positions: [
            {
              symbol: 'AAPL',
              marketValue: null as any,
              description: '',
              type: '',
              quantity: 0,
              lastPrice: 0,
              holdingPct: 0
            }
          ]
        }
      ]
    };

    presenter.sellAmountValidate(form, 'AAPL', mockAccountPositions);

    const amountControl = form.get('amount');
    expect(amountControl?.validator).toBeDefined();
  });

  it('should not apply validators if shareToggle is not UnitType.Dollars', () => {
    form.get('shareToggle')?.setValue(UnitType.Shares);

    const mockAccountPositions: AccountPositionsResponseInfo = {
      accountPositions: []
    };

    presenter.sellAmountValidate(form, 'AAPL', mockAccountPositions);

    const amountControl = form.get('amount');
    expect(amountControl?.validator).toBeNull(); // no validators applied
  });
});
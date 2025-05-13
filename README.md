import { FormBuilder, FormGroup, Validators } from '@angular/forms';
import { sellAmountValidate } from './your-presenter-file'; // Adjust the import path
import { ActionType, UnitType } from './your-enum-file'; // Adjust this import
import { amountValidator } from './your-validator-file'; // Adjust this import

describe('sellAmountValidate', () => {
  let fb: FormBuilder;
  let form: FormGroup;

  beforeEach(() => {
    fb = new FormBuilder();
    form = fb.group({
      amount: [''],
      shareToggle: [''],
      actionToggle: [''],
    });
  });

  it('should set required and validator with 0 if marketValue is null', () => {
    form.get('shareToggle')?.setValue(UnitType.Dollars);
    form.get('actionToggle')?.setValue(ActionType.Sell);

    const accountPositions = {
      accountPositions: [
        {
          positions: [
            {
              symbol: 'AAPL',
              marketValue: null,
            },
          ],
        },
      ],
    };

    sellAmountValidate(form, 'AAPL', accountPositions);

    const amountControl = form.get('amount');
    expect(amountControl?.validator).toBeTruthy();
    const errors = amountControl?.validator?.({ value: 100 });
    // Replace with actual logic from `amountValidator`
    expect(errors).toEqual(amountValidator(0)({ value: 100 }));
  });

  it('should apply validator with 90% of market value when valid', () => {
    form.get('shareToggle')?.setValue(UnitType.Dollars);
    form.get('actionToggle')?.setValue(ActionType.Sell);

    const accountPositions = {
      accountPositions: [
        {
          positions: [
            {
              symbol: 'AAPL',
              marketValue: 1000,
            },
          ],
        },
      ],
    };

    sellAmountValidate(form, 'AAPL', accountPositions);

    const amountControl = form.get('amount');
    const validatorFn = amountValidator(900); // 90% of 1000
    expect(amountControl?.validator).toBeTruthy();
    const errors = amountControl?.validator?.({ value: 1000 });
    expect(errors).toEqual(validatorFn({ value: 1000 }));
  });

  it('should not apply validator if actionToggle is not Sell or shareToggle is not Dollars', () => {
    form.get('shareToggle')?.setValue(UnitType.Shares);
    form.get('actionToggle')?.setValue(ActionType.Buy);

    const accountPositions = {
      accountPositions: [],
    };

    sellAmountValidate(form, 'AAPL', accountPositions);

    const amountControl = form.get('amount');
    expect(amountControl?.validator).toBeNull();
  });
});
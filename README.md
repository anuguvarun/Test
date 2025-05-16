import { FormGroup, FormControl } from '@angular/forms';
import { UnitType } from 'path-to-enums'; // Replace with actual path
import { presenter } from './path-to-presenter'; // Replace with actual path

describe('sellValidate', () => {
  let form: FormGroup;
  const mockAccountPositions = [
    {
      symbol: 'AAPL',
      marketValue: 1000,
      quantity: 50
    }
  ];

  beforeEach(() => {
    form = new FormGroup({
      shareToggle: new FormControl(UnitType.Dollars),
      amount: new FormControl(),
      quantity: new FormControl()
    });
  });

  it('should apply validator with 90% of marketValue when UnitType is Dollars', () => {
    presenter.sellValidate(form, 'AAPL', mockAccountPositions);
    const amountControl = form.get('amount');
    expect(amountControl?.validator).toBeDefined();

    const errors = amountControl?.validator?.({ value: 1001 });
    expect(errors).toBeTruthy(); // Should fail because 1001 > 900
  });

  it('should apply validator with quantity when UnitType is Shares', () => {
    form.get('shareToggle')?.setValue(UnitType.Shares);
    presenter.sellValidate(form, 'AAPL', mockAccountPositions);
    const quantityControl = form.get('quantity');
    expect(quantityControl?.validator).toBeDefined();

    const errors = quantityControl?.validator?.({ value: 51 });
    expect(errors).toBeTruthy(); // Should fail because 51 > 50
  });

  it('should apply validator with 0 if value is null', () => {
    const nullPosition = [{ symbol: 'AAPL' }];
    presenter.sellValidate(form, 'AAPL', nullPosition);
    const amountControl = form.get('amount');
    expect(amountControl?.validator).toBeDefined();

    const errors = amountControl?.validator?.({ value: 1 });
    expect(errors).toBeTruthy(); // Should fail because limit is 0
  });

  it('should not apply validators if shareToggle is undefined or invalid', () => {
    form.get('shareToggle')?.setValue(null);
    presenter.sellValidate(form, 'AAPL', mockAccountPositions);

    expect(form.get('amount')?.validator).toBeNull();
    expect(form.get('quantity')?.validator).toBeNull();
  });
});
import { FormGroup, FormControl, Validators } from '@angular/forms';
import { sellValidate } from './your-file';

describe('sellValidate', () => {
  it('should set required and custom validators on the form control', () => {
    const form = new FormGroup({
      shareToggle: new FormControl('Shares'),
      actionToggle: new FormControl('Sell'),
      quantity: new FormControl('')
    });

    const mockValidatorFn = jest.fn(value => value > 0);

    sellValidate(
      form,
      'AAPL',
      {
        accountPositions: [
          { symbol: 'AAPL', positions: [{ symbol: 'AAPL', value: 100 }] }
        ]
      },
      'Sell',
      'Shares',
      'quantity',
      'quantity',
      mockValidatorFn
    );

    form.get('quantity')?.setValue(10);
    expect(mockValidatorFn).toHaveBeenCalledWith(10);
  });
});
import { getMatchedPosition } from './your-file';

const mockAccountPositions = {
  accountPositions: [
    { symbol: 'AAPL', positions: [{ symbol: 'AAPL', value: 100 }] },
    { symbol: 'GOOG', positions: [{ symbol: 'GOOG', value: 150 }] }
  ]
};

describe('getMatchedPosition', () => {
  it('should return the correct position for the given symbol', () => {
    const result = getMatchedPosition(mockAccountPositions, 'AAPL');
    expect(result).toEqual({ symbol: 'AAPL', value: 100 });
  });

  it('should return undefined if the symbol is not found', () => {
    const result = getMatchedPosition(mockAccountPositions, 'TSLA');
    expect(result).toBeUndefined();
  });

  it('should handle undefined accountPositions gracefully', () => {
    const result = getMatchedPosition(undefined, 'AAPL');
    expect(result).toBeUndefined();
  });
});

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

import { FormGroup, FormControl, Validators } from '@angular/forms';
import { sellShareValidate } from './your-file';

describe('sellShareValidate', () => {
  it('should call sellValidate and apply Validators.max on the form control', () => {
    const form = new FormGroup({
      shareToggle: new FormControl('Shares'),
      actionToggle: new FormControl('Sell'),
      quantity: new FormControl('')
    });

    const mockPositions = {
      accountPositions: [
        { symbol: 'AAPL', positions: [{ symbol: 'AAPL', value: 100 }] }
      ]
    };

    sellShareValidate(form, 'AAPL', mockPositions);

    expect(form.get('quantity')?.validator).toBeTruthy();
  });
});


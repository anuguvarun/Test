import { FormControl } from '@angular/forms';
import { amountValidator } from './amount.validator';

describe('amountValidator', () => {
  it('should pass if amount is greater than minimum', () => {
    const validator = amountValidator(100);
    const control = new FormControl(150);
    expect(validator(control)).toBeNull();
  });

  it('should pass if amount is exactly the minimum', () => {
    const validator = amountValidator(100);
    const control = new FormControl(100);
    expect(validator(control)).toBeNull();
  });

  it('should fail if amount is less than minimum', () => {
    const validator = amountValidator(100);
    const control = new FormControl(50);
    expect(validator(control)).toEqual({
      amountValidator: {
        requiredValue: 100,
        actualValue: 50
      }
    });
  });

  it('should pass if value is NaN (non-numeric)', () => {
    const validator = amountValidator(100);
    const control = new FormControl('abc'); // becomes NaN
    expect(validator(control)).toBeNull();
  });

  it('should handle null or undefined values as valid', () => {
    const validator = amountValidator(100);
    expect(validator(new FormControl(null))).toBeNull();
    expect(validator(new FormControl(undefined))).toBeNull();
  });
});
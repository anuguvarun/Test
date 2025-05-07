import { AbstractControl } from '@angular/forms';
import { amountValidator } from './amount.validator'; // adjust this path as needed

describe('amountValidator', () => {
  const getMockControl = (value: any): AbstractControl => ({ value } as AbstractControl);

  it('should return null for a valid amount (less than minValue)', () => {
    const validator = amountValidator(100);
    const control = getMockControl(50);
    expect(validator(control)).toBeNull();
  });

  it('should return error object when value is greater than or equal to minValue', () => {
    const validator = amountValidator(100);
    const control = getMockControl(150);
    expect(validator(control)).toEqual({
      amountValidator: {
        requiredValue: 100,
        actualValue: 150
      }
    });
  });

  it('should return error object when value is equal to minValue', () => {
    const validator = amountValidator(100);
    const control = getMockControl(100);
    expect(validator(control)).toEqual({
      amountValidator: {
        requiredValue: 100,
        actualValue: 100
      }
    });
  });

  it('should return error object for NaN value', () => {
    const validator = amountValidator(100);
    const control = getMockControl('abc');
    expect(validator(control)).toEqual({
      amountValidator: {
        requiredValue: 100,
        actualValue: NaN
      }
    });
  });

  it('should return null for numeric string less than minValue', () => {
    const validator = amountValidator(100);
    const control = getMockControl('99');
    expect(validator(control)).toBeNull();
  });
});
import { FormControl } from '@angular/forms';
import { securityHeldValidator } from './path-to-your-validator-file';

describe('securityHeldValidator', () => {
  it('should return error if isHeld is true', () => {
    const validatorFn = securityHeldValidator(true);
    const control = new FormControl('123');
    const result = validatorFn(control);

    expect(result).toEqual({ securityHeldValidator: true });
  });

  it('should return null if isHeld is false', () => {
    const validatorFn = securityHeldValidator(false);
    const control = new FormControl('123');
    const result = validatorFn(control);

    expect(result).toBeNull();
  });

  it('should handle numeric values correctly', () => {
    const validatorFn = securityHeldValidator(true);
    const control = new FormControl(456);
    const result = validatorFn(control);

    expect(result).toEqual({ securityHeldValidator: true });
  });

  it('should still return null even if control value is zero and isHeld is false', () => {
    const validatorFn = securityHeldValidator(false);
    const control = new FormControl(0);
    const result = validatorFn(control);

    expect(result).toBeNull();
  });
});
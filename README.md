// duplicate-search.validator.spec.ts
import { FormControl, FormGroup } from '@angular/forms';
import { duplicateSearchValidator } from './duplicate-search.validator';

describe('duplicateSearchValidator', () => {
  const createFormGroup = (value: string) =>
    new FormGroup({
      search: new FormControl(value),
    });

  it('should return null if control value is empty', () => {
    const groups = [createFormGroup('Apple')];
    const validator = duplicateSearchValidator(0, groups);
    const control = new FormControl('');
    expect(validator(control)).toBeNull();
  });

  it('should return null if no duplicates are found', () => {
    const groups = [
      createFormGroup('Apple'),
      createFormGroup('Banana'),
      createFormGroup('Carrot'),
    ];
    const validator = duplicateSearchValidator(0, groups);
    const control = new FormControl('Durian');
    expect(validator(control)).toBeNull();
  });

  it('should detect a duplicate and return validation error', () => {
    const groups = [
      createFormGroup('Apple'),
      createFormGroup('Banana'),
      createFormGroup('apple'), // <-- duplicate of index 0
    ];
    const validator = duplicateSearchValidator(0, groups);
    const control = new FormControl('Apple');
    expect(validator(control)).toEqual({ duplicate: true });
  });

  it('should ignore case and trim whitespace when checking duplicates', () => {
    const groups = [
      createFormGroup('Apple'),
      createFormGroup('  apple  '), // <-- whitespace and case-insensitive duplicate
    ];
    const validator = duplicateSearchValidator(0, groups);
    const control = new FormControl(' APPLE ');
    expect(validator(control)).toEqual({ duplicate: true });
  });

  it('should not compare the current index with itself', () => {
    const groups = [createFormGroup('Apple')];
    const validator = duplicateSearchValidator(0, groups);
    const control = new FormControl('Apple');
    expect(validator(control)).toBeNull();
  });
});
import { AbstractControl, ValidatorFn } from '@angular/forms';

export function greaterThan(minValue: number): ValidatorFn {
  return (control: AbstractControl): { [key: string]: any } | null => {
    const value = +control.value;
    if (isNaN(value) || value <= minValue) {
      return { 'greaterThan': { requiredValue: minValue, actualValue: value } };
    }
    return null;
  };
}
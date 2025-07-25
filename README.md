import { Validators } from '@angular/forms'; // make sure this is imported

duplicateSearch(index: number): void {
  const search = this.cards[index].get('search');
  if (!search) return;

  const composedValidator = Validators.compose([
    search.validator,
    duplicateSearchValidator(index, this.cards)
  ]);

  search.setValidators(composedValidator);
  search.markAsTouched();
  search.updateValueAndValidity();
}
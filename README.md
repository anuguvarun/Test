const search = this.cards[index].get('search');

const existingValidator = search.validator; // This is the child's validators
const duplicateValidator = duplicateSearchValidator(index, this.cards);

// Merge them using compose
const mergedValidator = Validators.compose([
  existingValidator,
  duplicateValidator
]);

search.setValidators(mergedValidator);
search.markAsTouched();
search.updateValueAndValidity({ onlySelf: true });
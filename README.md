it('should return null if control value is null or empty', () => {
  const groups = [createFormGroup('Apple')];
  const validator = duplicateSearchValidator(0, groups);
  expect(validator(new FormControl(''))).toBeNull(); // covers line 7
});

it('should return null when no duplicates are found', () => {
  const groups = [
    createFormGroup('Apple'),
    createFormGroup('Banana'),
  ];
  const validator = duplicateSearchValidator(0, groups);
  expect(validator(new FormControl('Carrot'))).toBeNull(); // covers line 14
});

it('should return { duplicate: true } for a duplicate value', () => {
  const groups = [
    createFormGroup('Apple'),
    createFormGroup('apple'), // same as index 0
  ];
  const validator = duplicateSearchValidator(0, groups);
  expect(validator(new FormControl('apple'))).toEqual({ duplicate: true });
});
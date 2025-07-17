it('should set validators, update value and mark search control as touched', () => {
  const fb = new FormBuilder();

  // Mock duplicateSearchValidator
  const mockValidator = jest.fn().mockReturnValue((control: AbstractControl) => null);
  component['duplicateSearchValidator'] = mockValidator;

  // Setup component.cards with FormGroups
  const searchControl = fb.control('');
  const formGroup = fb.group({ search: searchControl });

  component.cards = [formGroup];
  
  // Spy on the relevant methods
  const setValidatorsSpy = jest.spyOn(searchControl, 'setValidators');
  const updateSpy = jest.spyOn(searchControl, 'updateValueAndValidity');
  const markSpy = jest.spyOn(searchControl, 'markAsTouched');

  // Call method under test
  component.duplicateSearch(0);

  expect(setValidatorsSpy).toHaveBeenCalledWith([
    Validators.required,
    expect.any(Function)
  ]);
  expect(updateSpy).toHaveBeenCalled();
  expect(markSpy).toHaveBeenCalled();
});
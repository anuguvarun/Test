it('should update fields with markAsDirty, markAsUntouched and updateValueAndValidity', () => {
  const presenter = new StepCardPresenter();

  const mockForm = new FormGroup({
    fieldA: new FormControl(''),
    fieldB: new FormControl('')
  });

  const fieldList = ['fieldA', 'fieldB'];

  // Spy on methods
  const spyDirty = jest.spyOn(mockForm.get('fieldA')!, 'markAsDirty');
  const spyUntouched = jest.spyOn(mockForm.get('fieldA')!, 'markAsUntouched');
  const spyUpdate = jest.spyOn(mockForm.get('fieldA')!, 'updateValueAndValidity');

  presenter.updateFormFieldStatus(mockForm, fieldList, false);

  expect(spyDirty).toHaveBeenCalled();
  expect(spyUntouched).toHaveBeenCalledWith({ onlySelf: true });
  expect(spyUpdate).toHaveBeenCalled();
});

it('should reset fields when isReset is true', () => {
  const presenter = new StepCardPresenter();

  const mockForm = new FormGroup({
    fieldA: new FormControl('some value')
  });

  const spyReset = jest.spyOn(mockForm.get('fieldA')!, 'reset');

  presenter.updateFormFieldStatus(mockForm, ['fieldA'], true);

  expect(spyReset).toHaveBeenCalled();
});

it('should emit and focus input when matching security is found', () => {
  jest.useFakeTimers(); // Setup fake timers

  const index = 0;

  const setErrorsSpy = jest.spyOn(component.search, 'setErrors');
  const onSearchSelectEmitSpy = jest.spyOn(component, 'onSearchSelectEmit');

  component.search.setValue('A');
  component.searchOptions = [{ cusip: 'T12345678', value: 'A', description: '' }];

  // Mocks for document interaction
  const focusMock = jest.fn();
  const querySelectorMock = jest.fn().mockReturnValue({ focus: focusMock });

  const getElementByIdMock = jest
    .spyOn(document, 'getElementById')
    .mockReturnValue({ querySelector: querySelectorMock } as any);

  // Run the method
  component.securityPriceSearchOnBlur(index);

  // Check immediate behavior
  expect(setErrorsSpy).not.toHaveBeenCalled();
  expect(onSearchSelectEmitSpy).toHaveBeenCalledTimes(1);

  // Execute any pending setTimeout
  jest.runAllTimers();

  // Validate DOM behavior
  expect(getElementByIdMock).toHaveBeenCalledWith('actionToggle' + index);
  expect(querySelectorMock).toHaveBeenCalledWith(
    'input, [tabindex]:not([tabindex="-1"])'
  );
  expect(focusMock).toHaveBeenCalledTimes(1);
});
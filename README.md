it('should call onSearchSelectEmit and focus the next input when matching security is found', () => {
  jest.useFakeTimers();

  const index = 0;

  // Spies on component methods
  const setErrorsSpy = jest.spyOn(component.search, 'setErrors');
  const onSearchSelectEmitSpy = jest.spyOn(component, 'onSearchSelectEmit');

  // Set test data
  component.search.setValue('A');
  component.searchOptions = [{ cusip: 'T12345678', value: 'A', description: '' }];

  // Mock focusable element
  const focusMock = jest.fn();
  const querySelectorMock = jest.fn().mockReturnValue({ focus: focusMock });

  const getElementByIdMock = jest.fn().mockReturnValue({
    querySelector: querySelectorMock
  });

  // Inject the mocked document into component
  component.document = { getElementById: getElementByIdMock } as any;

  // Call the method under test
  component.securityPriceSearchOnBlur(index);

  // Verify synchronous logic
  expect(setErrorsSpy).not.toHaveBeenCalled();
  expect(onSearchSelectEmitSpy).toHaveBeenCalledTimes(1);

  // Advance the timer to trigger setTimeout
  jest.runAllTimers();

  // Verify DOM interaction
  expect(getElementByIdMock).toHaveBeenCalledWith('actionToggle' + index);
  expect(querySelectorMock).toHaveBeenCalledWith('input, [tabindex]:not([tabindex="-1"])');
  expect(focusMock).toHaveBeenCalledTimes(1);
});
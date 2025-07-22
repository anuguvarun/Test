it('should call onSearchSelectEmit and focus the next input when matching security is found', () => {
  jest.useFakeTimers();

  const index = 0;

  const setErrorsSpy = jest.spyOn(component.search, 'setErrors');
  const onSearchSelectEmitSpy = jest.spyOn(component, 'onSearchSelectEmit');

  component.search.setValue('A');
  component.searchOptions = [{ cusip: 'T12345678', value: 'A', description: '' }];

  // Full DOM Mocking (global level)
  const focusMock = jest.fn();
  const querySelectorMock = jest.fn().mockReturnValue({ focus: focusMock });
  const getElementByIdMock = jest.spyOn(document, 'getElementById').mockReturnValue({
    querySelector: querySelectorMock
  } as any);

  component.securityPriceSearchOnBlur(index);
  expect(setErrorsSpy).not.toHaveBeenCalled();
  expect(onSearchSelectEmitSpy).toHaveBeenCalledTimes(1);

  // Trigger the focus logic
  jest.runAllTimers();

  expect(getElementByIdMock).toHaveBeenCalledWith('actionToggle' + index);
  expect(querySelectorMock).toHaveBeenCalledWith('input, [tabindex]:not([tabindex="-1"])');
  expect(focusMock).toHaveBeenCalledTimes(1);
});
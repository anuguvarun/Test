jest.useFakeTimers();

it('...should focus and call methods correctly', () => {
  const setErrorSpy = jest.spyOn(component.search, 'setErrors');
  const onSearchSelectEmitSpy = jest.spyOn(component, 'onSearchSelectEmit');

  component.search.setValue('A');
  component.searchOptions = [{ cusip: 'T12345678', value: 'A', description: '' }];

  component.securityPriceSearchOnBlur(index);

  expect(setErrorSpy).not.toHaveBeenCalled();
  expect(onSearchSelectEmitSpy).toHaveBeenCalledTimes(1);

  const elementSpy = jest.spyOn(documentStub, 'getElementById');
  const element = documentStub.getElementById('actionToggle' + index);

  const querySelectorSpy = jest.spyOn(element, 'querySelector');
  const focusableElementSpy = jest.spyOn(element.querySelector(''), 'focus');

  // Advance timers
  jest.runAllTimers();

  expect(elementSpy).toHaveBeenCalledTimes(1);
  expect(querySelectorSpy).toHaveBeenCalledWith(
    'input, [tabindex]:not([tabindex="-1"])'
  );
  expect(focusableElementSpy).toHaveBeenCalledTimes(1);
});
it('securityPriceSearchOnBlur calls expected methods if searchOptions is present with a security match', () => {
  // Mock methods
  const setErrorSpy = jest.spyOn(component.search, 'setErrors');
  const onSearchSelectEmitSpy = jest.spyOn(component, 'onSearchSelectEmit');
  const getElementByIdSpy = jest.spyOn(document, 'getElementById').mockImplementation((id) => {
    return {
      querySelector: jest.fn(() => ({
        focus: jest.fn(),
      })),
    } as unknown as HTMLElement;
  });

  // Mock initial values
  component.search.setValue('A');
  component.searchOptions = [{ cusip: 'T12345678', value: 'A', description: "" }];
  
  // Call the function
  component.securityPriceSearchOnBlur(0);

  // Assertions
  expect(setErrorSpy).not.toHaveBeenCalled(); // Ensure setErrors is not called
  expect(onSearchSelectEmitSpy).toHaveBeenCalledTimes(1); // Ensure onSearchSelectEmit is called

  // Simulate delayed execution (setTimeout)
  setTimeout(() => {
    expect(getElementByIdSpy).toHaveBeenCalledWith('actionToggle0'); // Ensure getElementById is called with the correct ID

    const element = getElementByIdSpy.mock.results[0].value;
    const querySelectorSpy = jest.spyOn(element, 'querySelector');
    const focusFn = element.querySelector().focus;

    expect(querySelectorSpy).toHaveBeenCalledWith('input, [tabindex]:not([tabindex="-1"])'); // Ensure querySelector is called
    expect(focusFn).toHaveBeenCalledTimes(1); // Ensure focus is called
  }, 500);
});
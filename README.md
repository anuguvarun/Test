jest.spyOn(Object.getPrototypeOf(component).document, 'getElementById')
  .mockReturnValue({ querySelector: querySelectorMock } as any);
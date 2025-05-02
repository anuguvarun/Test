it('should call updateValueAndValidity with tradeAgreement', () => {
  const updateSpy = jest.spyOn(component.tradeAgreement, 'updateValueAndValidity');
  jest.spyOnProperty(component.tradeAgreement, 'valid', 'get').mockReturnValue(false);

  component.isTradeAgreementValid();

  expect(updateSpy).toHaveBeenCalled();
});
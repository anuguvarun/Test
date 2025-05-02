it('should not emit orderSubmitted if tradeAgreement is not valid', () => {
  jest.spyOnProperty(component.tradeAgreement, 'valid', 'get').mockReturnValue(false);
  const emitSpy = jest.spyOn(component.orderSubmitted, 'emit');

  component.isTradeAgreementValid();

  expect(emitSpy).not.toHaveBeenCalled();
});
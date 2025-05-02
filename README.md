it('should emit orderSubmitted if tradeAgreement is valid', () => {
  jest.spyOnProperty(component.tradeAgreement, 'valid', 'get').mockReturnValue(true); // ✅ FIXED
  const emitSpy = jest.spyOn(component.orderSubmitted, 'emit');
  const mockOrder = { some: 'order' };

  const createOrderSpy = jest
    .spyOn(TradeOrderConfirmPresenter.prototype, 'createOrder')
    .mockReturnValue(mockOrder);

  component.isTradeAgreementValid();

  expect(createOrderSpy).toHaveBeenCalledWith(component.cardValues, component.accountNumber);
  expect(emitSpy).toHaveBeenCalledWith(mockOrder);
});
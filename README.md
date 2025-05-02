it('should call updateValueAndValidity on tradeAgreement', () => {
  const updateSpy = jest.spyOn(component.tradeAgreement, 'updateValueAndValidity');
  component.tradeAgreement.valid = false;

  component.isTradeAgreementValid();

  expect(updateSpy).toHaveBeenCalled();
});

it('should emit orderSubmitted if tradeAgreement is valid', () => {
  component.tradeAgreement.valid = true;
  const emitSpy = jest.spyOn(component.orderSubmitted, 'emit');
  const mockOrder = { some: 'order' };

  const createOrderSpy = jest.spyOn(TradeOrderConfirmPresenter.prototype, 'createOrder')
    .mockReturnValue(mockOrder);

  component.isTradeAgreementValid();

  expect(createOrderSpy).toHaveBeenCalledWith(component.cardValues, component.accountNumber);
  expect(emitSpy).toHaveBeenCalledWith(mockOrder);
});

it('should not emit orderSubmitted if tradeAgreement is not valid', () => {
  component.tradeAgreement.valid = false;
  const emitSpy = jest.spyOn(component.orderSubmitted, 'emit');

  component.isTradeAgreementValid();

  expect(emitSpy).not.toHaveBeenCalled();
});
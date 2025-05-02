jest.spyOnProperty(component.tradeAgreement, 'valid', 'get').mockReturnValue(true);
const emitSpy = jest.spyOn(component.orderSubmitted, 'emit');

const mockOrder: Trade = {
  accountNumber: '12345',
  brokerageAccountNumber: '98765',
  partyId: 'P001',
  sourceChannelCode: 'WEB',
  trades: [],
};

const createOrderSpy = jest
  .spyOn(TradeOrderConfirmPresenter.prototype, 'createOrder')
  .mockReturnValue(mockOrder);

component.isTradeAgreementValid();

expect(createOrderSpy).toHaveBeenCalledWith(component.cardValues, component.accountNumber);
expect(emitSpy).toHaveBeenCalledWith(mockOrder);
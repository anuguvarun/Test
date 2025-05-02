mockSecuritiesFacade = {
  getSecurities: jest.fn(),
  getSecurityPrice: jest.fn(),
  resetSecurities: jest.fn(),
  resetSubState: jest.fn(),
};


it('should call getSecurities on securityFacade when onSearch is called', () => {
  const mockSecurity = { index: 0, security: {} } as any;
  container.onSearch(mockSecurity);
  expect(mockSecuritiesFacade.getSecurities).toHaveBeenCalledWith(mockSecurity);
});

it('should call getSecurityPrice on securityFacade when onSearchSelect is called', () => {
  const mockSecurity = { index: 1, security: {} } as any;
  container.onSearchSelect(mockSecurity);
  expect(mockSecuritiesFacade.getSecurityPrice).toHaveBeenCalledWith(mockSecurity);
});

it('should call resetSecurities on securityFacade when onSelectTradeType is called', () => {
  const mockSecurity = { index: 2, security: {} } as any;
  container.onSelectTradeType(mockSecurity);
  expect(mockSecuritiesFacade.resetSecurities).toHaveBeenCalledWith(mockSecurity);
});

it('should call resetSubState on securityFacade when onRemoveCard is called', () => {
  const mockIndex = 3;
  container.onRemoveCard(mockIndex);
  expect(mockSecuritiesFacade.resetSubState).toHaveBeenCalledWith(mockIndex);
});
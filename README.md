describe('RequestTradeContainer', () => {
  // ... existing setup remains untouched

  it('should call setAccountName on tradeRecommendationFacade when accountName input is set', () => {
    const mockValue = 'Mock Account';
    container.accountName = mockValue;
    expect(mockTradeRecommendationFacade.setAccountName).toHaveBeenCalledWith(mockValue);
  });

  it('should call setAccountNumber on tradeRecommendationFacade when accountNumber input is set', () => {
    const mockValue = 12345;
    container.accountNumber = mockValue;
    expect(mockTradeRecommendationFacade.setAccountNumber).toHaveBeenCalledWith(mockValue);
  });

  it('should call setHeaderLinkText on tradeRecommendationFacade when linkText input is set', () => {
    const mockValue = 'Link Text';
    container.linkText = mockValue;
    expect(mockTradeRecommendationFacade.setHeaderLinkText).toHaveBeenCalledWith(mockValue);
  });

  it('should call setHeaderLinkRoute on tradeRecommendationFacade when linkStateName input is set', () => {
    const mockValue = 'route-name';
    container.linkStateName = mockValue;
    expect(mockTradeRecommendationFacade.setHeaderLinkRoute).toHaveBeenCalledWith(mockValue);
  });

  it('should call setSteps on tradeRecommendationFacade when steps input is set', () => {
    const mockSteps = [{ title: 'Step 1', index: 0 }];
    container.steps = mockSteps;
    expect(mockTradeRecommendationFacade.setSteps).toHaveBeenCalledWith(mockSteps);
  });

  it('should call setPartyId on tradeFacade when partyId input is set', () => {
    const mockValue = 789;
    container.partyId = mockValue;
    expect(mockTradeFacade.setPartyId).toHaveBeenCalledWith(mockValue);
  });
});
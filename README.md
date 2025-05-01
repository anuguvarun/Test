describe('queryChange', () => {
  it('should call getTradeOrderHistory with merged defaults and overrides', () => {
    component.accountNumber = 123;
    component.page = 1;
    component.pageSize = 10;

    const queryInput: Partial<TransactionQuery> = {
      accountNumber: 999,
      fromDate: '2025-01-01',
      endDate: '2025-02-01',
      sortBy: SortColumn.createdDate,
      sortOrder: SortDirection.asc
    };

    const expectedMergedQuery = {
      accountNumber: 999,
      page: 1,
      size: 10,
      fromDate: '2025-01-01',
      endDate: '2025-02-01',
      sortBy: SortColumn.createdDate,
      sortOrder: SortDirection.asc
    };

    const spy = jest.spyOn(tradeOrderHistoryFacade, 'getTradeOrderHistory');
    component.queryChange(queryInput);
    expect(spy).toHaveBeenCalledWith(expectedMergedQuery);
  });

  it('should fall back to defaults when values are missing in input', () => {
    component.accountNumber = 123;
    component.page = 2;
    component.pageSize = 20;

    const queryInput: Partial<TransactionQuery> = {};

    const spy = jest.spyOn(tradeOrderHistoryFacade, 'getTradeOrderHistory');
    component.queryChange(queryInput);

    expect(spy).toHaveBeenCalledWith({
      accountNumber: 123,
      page: 2,
      size: 20,
      fromDate: expect.any(String), // assuming currentYearStartDate is a string
      endDate: expect.any(String),  // assuming currentDate is a string
      sortBy: SortColumn.submittedDate,
      sortOrder: SortDirection.desc
    });
  });
});
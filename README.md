lgetTradesSummary(query?: { fromDate?: string; endDate?: string })
  : Observable<TradeOrderSummaryResponse> {

  const endpoint = `${this.envService.map.baseUrl}/admin/tradeOrder/summary`;

  let params = new HttpParams();

  if (query?.fromDate) {
    params = params.set('fromDate', query.fromDate);
  }

  if (query?.endDate) {
    params = params.set('endDate', query.endDate);
  }

  return this.envService.map.production
    ? this.httpClient.get<TradeOrderSummaryResponse>(endpoint, { params })
    : of({});
}
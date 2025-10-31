def "processTradeOrder sends email when service succeeds"() {
    given:
    long tradeId = 12345L
    def req = GroovyUtils.getTradeOrderRequest()
    Mockito.when(tradeOrderProcessService.processTradeOrder(any(TradeOrderRequest), eq(tradeId)))
           .thenReturn(new WorkItemResponse("W123456", "SUCCESS"))

    when:
    def result = postTrade(tradeId, req)

    then:
    result.response.status == HttpStatus.OK.value()
    Mockito.verify(emailService, Mockito.times(1))
           .sendReport(contains("Trade Request Id: " + tradeId))
}

def "processTradeOrder sends email even when service throws"() {
    given:
    long tradeId = 12345L
    def req = GroovyUtils.getTradeOrderRequest()
    Mockito.when(tradeOrderProcessService.processTradeOrder(any(TradeOrderRequest), eq(tradeId)))
           .thenThrow(new RuntimeException("boom"))

    when:
    def result = postTrade(tradeId, req)

    then:
    result.response.status == HttpStatus.OK.value()
    result.response.contentAsString == "" || result.response.contentAsString == "null"
    Mockito.verify(emailService, Mockito.times(1))
           .sendReport(contains("Trade Request Id: " + tradeId))
}

def "processTradeOrder returns 200 even when email sending fails"() {
    given:
    long tradeId = 12345L
    def req = GroovyUtils.getTradeOrderRequest()
    Mockito.when(tradeOrderProcessService.processTradeOrder(any(TradeOrderRequest), eq(tradeId)))
           .thenReturn(new WorkItemResponse("W999", "SUCCESS"))
    Mockito.doThrow(new RuntimeException("smtp down"))
           .when(emailService).sendReport(anyString())

    when:
    def result = postTrade(tradeId, req)

    then:
    result.response.status == HttpStatus.OK.value()
    Mockito.verify(emailService, Mockito.times(1)).sendReport(anyString())
}

def "processTradeOrder returns 400 for invalid request and does not send email"() {
    given:
    long tradeId = 12345L
    def req = GroovyUtils.getTradeOrderRequest()
    req.accountNumber = null  // violate @Valid

    when:
    def result = postTrade(tradeId, req)

    then:
    result.response.status == HttpStatus.BAD_REQUEST.value()
    Mockito.verify(emailService, Mockito.never()).sendReport(anyString())
    Mockito.verify(tradeOrderProcessService, Mockito.never())
           .processTradeOrder(any(TradeOrderRequest), anyLong())
}
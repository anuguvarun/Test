def "should send email report after successful trade order processing"() {
    given:
    def service = Mock(TradeService)
    def emailService = Mock(EmailService)
    def log = Mock(Logger)
    def tradeHandler = new TradeHandler(service: service, emailService: emailService, log: log)
    def tradeOrderRequest = new TradeOrderRequest()
    def id = 123

    when:
    tradeHandler.handleTrade(tradeOrderRequest, id)

    then:
    1 * service.processTradeOrder(tradeOrderRequest, id)
    1 * emailService.sendReport("Trade Request Id:" + id)
    0 * log.error(_, _, _)
}
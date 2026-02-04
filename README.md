import spock.lang.Specification

import java.time.LocalDateTime
import java.time.ZoneId

class TradeOrderAdminServiceImplSpec extends Specification {

    def tradeOrderRepository = Mock(TradeOrderRepository)
    def service = new TradeOrderAdminServiceImpl(tradeOrderRepository: tradeOrderRepository)

    def "getSummary returns counts from repository map and formatted lastUpdated"() {
        given: "repository returns both summary counts"
        def summaryMap = [
                (TradeOrderSummaryConstants.UN_APPROVED_COUNT): 12L,
                (TradeOrderSummaryConstants.UN_SETTLED_COUNT) : 7L
        ]
        tradeOrderRepository.getSummaryCount() >> summaryMap

        and: "time is fixed in America/New_York"
        GroovyMock(LocalDateTime, global: true)
        def nyZone = ZoneId.of("America/New_York")
        def fixedNow = LocalDateTime.of(2026, 2, 4, 10, 15, 30)
        LocalDateTime.now({ ZoneId z -> z == nyZone }) >> fixedNow

        and: "expected formatted date"
        def expectedFormatted = fixedNow.format(RepositoryHelperConstants.dateTimeFormatter)

        when:
        def result = service.getSummary()

        then: "repo called once"
        1 * tradeOrderRepository.getSummaryCount()

        and: "counts are mapped"
        result.unApprovedCount == 12L
        result.unSettledCount == 7L

        and: "lastUpdated is formatted with the shared formatter"
        result.lastUpdated == expectedFormatted
    }

    def "getSummary defaults missing counts to 0L"() {
        given: "repository returns empty map (no keys)"
        tradeOrderRepository.getSummaryCount() >> [:]

        and: "fixed time"
        GroovyMock(LocalDateTime, global: true)
        def nyZone = ZoneId.of("America/New_York")
        def fixedNow = LocalDateTime.of(2026, 2, 4, 0, 0, 0)
        LocalDateTime.now({ ZoneId z -> z == nyZone }) >> fixedNow

        when:
        def result = service.getSummary()

        then:
        result.unApprovedCount == 0L
        result.unSettledCount == 0L
        result.lastUpdated == fixedNow.format(RepositoryHelperConstants.dateTimeFormatter)
    }

    def "getSummary uses America/New_York timezone when getting time"() {
        given:
        tradeOrderRepository.getSummaryCount() >> [:]

        and:
        GroovyMock(LocalDateTime, global: true)
        def fixedNow = LocalDateTime.of(2026, 2, 4, 1, 2, 3)

        when:
        service.getSummary()

        then: "assert the ZoneId argument used in now(...)"
        1 * LocalDateTime.now({ ZoneId z -> z.id == "America/New_York" }) >> fixedNow
    }

    def "getSummary behavior when map contains a key with null value (documents current behavior)"() {
        given: "map contains key but value is null"
        tradeOrderRepository.getSummaryCount() >> [
                (TradeOrderSummaryConstants.UN_APPROVED_COUNT): null,
                (TradeOrderSummaryConstants.UN_SETTLED_COUNT) : 5L
        ]

        and:
        GroovyMock(LocalDateTime, global: true)
        def nyZone = ZoneId.of("America/New_York")
        def fixedNow = LocalDateTime.of(2026, 2, 4, 3, 4, 5)
        LocalDateTime.now({ ZoneId z -> z == nyZone }) >> fixedNow

        when:
        def result = service.getSummary()

        then:
        // NOTE: Map.getOrDefault returns null if the key exists with a null value.
        // If you don't want nulls, you should defensively coalesce to 0L in the service.
        result.unApprovedCount == null
        result.unSettledCount == 5L
    }
}
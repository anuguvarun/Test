package org.fc.trade.order.request.orchestrator.controller

import com.fasterxml.jackson.databind.ObjectMapper
import org.fc.trade.order.request.orchestrator.model.TradeOrderRequest
import org.fc.trade.order.request.orchestrator.model.WorkItemResponse
import org.fc.trade.order.request.orchestrator.service.TradeOrderProcessService
import org.fc.trade.order.request.orchestrator.service.EmailSenderService
import org.junit.runner.RunWith
import org.mockito.Mockito
import org.springframework.beans.factory.annotation.Autowired
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc
import org.springframework.boot.test.context.SpringBootTest
import org.springframework.boot.test.mock.mockito.MockitoBean   // Boot 3.4+
import org.springframework.http.HttpStatus
import org.springframework.http.MediaType
import org.springframework.test.context.ActiveProfiles
import org.springframework.test.context.junit4.SpringRunner
import org.springframework.test.web.servlet.MockMvc
import org.springframework.test.web.servlet.MvcResult
import spock.lang.Specification

import static org.mockito.ArgumentMatchers.*
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.post
import static org.springframework.test.web.servlet.result.MockMvcResultHandlers.print

@ActiveProfiles(["test"])
@RunWith(SpringRunner)
@SpringBootTest
@AutoConfigureMockMvc
class TradeOrderProcessControllerEmailSpec extends Specification {

    @Autowired MockMvc mockMvc
    ObjectMapper mapper = new ObjectMapper()

    // Controller collaborators
    @MockitoBean TradeOrderProcessService tradeOrderProcessService
    @MockitoBean EmailSenderService emailService

    // ----------------------------
    // 1) service succeeds -> email sent, 200
    // ----------------------------
    def "processTradeOrder sends email when service succeeds"() {
        given:
        long tradeId = 12345L
        TradeOrderRequest req = GroovyUtils.getTradeOrderRequest()
        Mockito.when(tradeOrderProcessService.processTradeOrder(any(TradeOrderRequest), eq(tradeId)))
               .thenReturn(new WorkItemResponse("W123456", "SUCCESS"))

        when:
        MvcResult result = postTrade(tradeId, req)

        then:
        result.response.status == HttpStatus.OK.value()
        Mockito.verify(emailService, Mockito.times(1))
               .sendReport(contains("Trade Request Id: " + tradeId))
    }

    // ----------------------------
    // 2) service throws -> email still sent (finally), 200 with empty/null body
    // ----------------------------
    def "processTradeOrder sends email even when service throws"() {
        given:
        long tradeId = 12345L
        TradeOrderRequest req = GroovyUtils.getTradeOrderRequest()
        Mockito.when(tradeOrderProcessService.processTradeOrder(any(TradeOrderRequest), eq(tradeId)))
               .thenThrow(new RuntimeException("boom"))

        when:
        MvcResult result = postTrade(tradeId, req)

        then:
        result.response.status == HttpStatus.OK.value()
        result.response.contentAsString == "" || result.response.contentAsString == "null"
        Mockito.verify(emailService, Mockito.times(1))
               .sendReport(contains("Trade Request Id: " + tradeId))
    }

    // ----------------------------
    // 3) email throws -> controller still returns 200 (caught in finally)
    // ----------------------------
    def "processTradeOrder returns 200 even when email sending fails"() {
        given:
        long tradeId = 12345L
        TradeOrderRequest req = GroovyUtils.getTradeOrderRequest()
        Mockito.when(tradeOrderProcessService.processTradeOrder(any(TradeOrderRequest), eq(tradeId)))
               .thenReturn(new WorkItemResponse("W999", "SUCCESS"))
        Mockito.doThrow(new RuntimeException("smtp down"))
               .when(emailService).sendReport(anyString())

        when:
        MvcResult result = postTrade(tradeId, req)

        then:
        result.response.status == HttpStatus.OK.value()
        Mockito.verify(emailService, Mockito.times(1)).sendReport(anyString())
    }

    // ----------------------------
    // 4) validation failure -> 400 and email NOT called
    // (fails before hitting the method body, so finally doesn't execute)
    // ----------------------------
    def "processTradeOrder returns 400 for invalid request and does not send email"() {
        given:
        long tradeId = 12345L
        // make payload invalid; adjust these field names if needed for your @Valid rules
        TradeOrderRequest req = GroovyUtils.getTradeOrderRequest()
        req.accountNumber = null

        when:
        MvcResult result = postTrade(tradeId, req)

        then:
        result.response.status == HttpStatus.BAD_REQUEST.value()
        Mockito.verify(emailService, Mockito.never()).sendReport(anyString())
        Mockito.verify(tradeOrderProcessService, Mockito.never())
               .processTradeOrder(any(TradeOrderRequest), anyLong())
    }

    // helper
    private MvcResult postTrade(long tradeId, TradeOrderRequest req) {
        def uri = "/tradeOrder/${tradeId}/execute"
        mockMvc.perform(
                post(uri)
                        .content(mapper.writeValueAsString(req))
                        .contentType(MediaType.APPLICATION_JSON)
                        .accept(MediaType.APPLICATION_JSON)
        ).andDo(print()).andReturn()
    }
}
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
import org.springframework.boot.test.mock.mockito.MockBean
import org.springframework.http.HttpStatus
import org.springframework.http.MediaType
import org.springframework.security.core.GrantedAuthority
import org.springframework.security.core.context.SecurityContextHolder
import org.springframework.test.context.ActiveProfiles
import org.springframework.test.context.junit4.SpringRunner
import org.springframework.test.web.servlet.MockMvc
import org.springframework.test.web.servlet.MvcResult
import org.springframework.test.web.servlet.setup.MockMvcBuilders
import org.springframework.web.context.WebApplicationContext
import spock.lang.Specification

import static org.mockito.ArgumentMatchers.*
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.post
import static org.springframework.test.web.servlet.result.MockMvcResultHandlers.print
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*

@RunWith(SpringRunner)
@SpringBootTest
@AutoConfigureMockMvc
@ActiveProfiles(["test"])
class TradeOrderProcessControllerIntegrationSpec extends Specification {

    private MockMvc mockMvc
    ObjectMapper mapper

    @Autowired
    WebApplicationContext webApplicationContext

    @MockBean
    TradeOrderProcessService tradeOrderProcessService

    // 👇 NEW: mock the email sender
    @MockBean
    EmailSenderService emailService

    List<GrantedAuthority> authorities = []

    def setup() {
        mapper = new ObjectMapper()
        SecurityContextHolder.clearContext()
        mockMvc = MockMvcBuilders.webAppContextSetup(webApplicationContext).build()
    }

    // ------------------------------------------------------------
    // ✅ 200 + email sent with workItemId present
    // ------------------------------------------------------------
    def "TradeOrderRequest returns 200 and sends email (workItem present)"() {
        given:
        def tradeId = 12345L
        TradeOrderRequest request = GroovyUtils.getTradeOrderRequest()
        def workItemId = "W123456"
        Mockito.when(tradeOrderProcessService.processTradeOrder(any(TradeOrderRequest), eq(tradeId)))
               .thenReturn(new WorkItemResponse(workItemId, "SUCCESS"))

        when:
        def result = makeTradeOrderRequest(request, tradeId)

        then:
        result.response.status == HttpStatus.OK.value()
        Mockito.verify(emailService, Mockito.times(1))
               .sendReport(argThat { String s -> s.contains("Trade Request Id: " + tradeId) && s.contains(workItemId) })
    }

    // ------------------------------------------------------------
    // ✅ 200 + email sent when workItemId is null
    // ------------------------------------------------------------
    def "TradeOrderRequest returns 200 and sends email (workItem null)"() {
        given:
        def tradeId = 12345L
        TradeOrderRequest request = GroovyUtils.getTradeOrderRequest()
        Mockito.when(tradeOrderProcessService.processTradeOrder(any(TradeOrderRequest), eq(tradeId)))
               .thenReturn(new WorkItemResponse(null, "SUCCESS"))

        when:
        def result = makeTradeOrderRequest(request, tradeId)

        then:
        result.response.status == HttpStatus.OK.value()
        Mockito.verify(emailService, Mockito.times(1))
               .sendReport(argThat { String s -> s.contains("Trade Request Id: " + tradeId) })
    }

    // ------------------------------------------------------------
    // ✅ email still sent even if the service throws
    // (controller catches, returns 200 with empty body in current code)
    // ------------------------------------------------------------
    def "Email sent even when service throws"() {
        given:
        def tradeId = 12345L
        TradeOrderRequest request = GroovyUtils.getTradeOrderRequest()
        Mockito.when(tradeOrderProcessService.processTradeOrder(any(TradeOrderRequest), eq(tradeId)))
               .thenThrow(new RuntimeException("boom"))

        when:
        def result = makeTradeOrderRequest(request, tradeId)

        then:
        result.response.status == HttpStatus.OK.value()
        // content is empty because response is null in controller
        result.response.contentAsString == "" || result.response.contentAsString == "null"
        Mockito.verify(emailService, Mockito.times(1))
               .sendReport(argThat { String s -> s.contains("Trade Request Id: " + tradeId) })
    }

    // ------------------------------------------------------------
    // ❌ 400 for invalid payload (table-driven like your screenshot)
    // ------------------------------------------------------------
    def "invalid TradeOrderRequest returns 400"() {
        given:
        TradeOrderRequest request = GroovyUtils.getTradeOrderRequest()
        // break one field per row
        request.accountNumber = accountNumber as Long
        request.brokerageAccountNumber = brokerageAccountNumber
        request.trades.get(0).symbol = symbol
        request.trades.get(0).cusip = cusip

        when:
        def result = makeTradeOrderRequest(request, 12345L)

        then:
        result.response.status == HttpStatus.BAD_REQUEST.value()
        Mockito.verify(emailService, Mockito.never()).sendReport(anyString())

        where:
        accountNumber | brokerageAccountNumber | symbol | cusip
        null          | "660800613"            | "AAPL" | "10110101"
        1176          | null                   | "AAPL" | "10110101"
        1176          | "660800613"            | null   | "10110101"
        1176          | "660800613"            | "AAPL" | null
    }

    // ------------------------------------------------------------
    // ❌ 403 when auth is denied (mirrors your existing spec)
    // ------------------------------------------------------------
    def "access is denied for authentication other than oauth"() {
        given:
        TradeOrderRequest request = GroovyUtils.getTradeOrderRequest()
        // however you set up your non-allowed auth in your project:
        def auth = new FCUserAuthentication(userId: "1234", details: "NCEWP", authorities: authorities)
        SecurityContextHolder.getContext().setAuthentication(auth)

        when:
        def result = makeTradeOrderRequest(request, 12345L)

        then:
        result.response != null
        result.response.status == HttpStatus.FORBIDDEN.value()
        Mockito.verify(emailService, Mockito.never()).sendReport(anyString())
    }

    // ------------------------------------------------------------
    // helper to post to the controller
    // ------------------------------------------------------------
    private MvcResult makeTradeOrderRequest(TradeOrderRequest request, long tradeId) {
        String uri = "/tradeOrder/${tradeId}/execute"
        return mockMvc.perform(
                post(uri)
                        .content(getJsonFromObject(request))
                        .contentType(MediaType.APPLICATION_JSON)
                        .accept(MediaType.APPLICATION_JSON)
        ).andDo(print())
         .andReturn()
    }

    private String getJsonFromObject(Object obj) {
        return mapper.writeValueAsString(obj)
    }
}
package org.fc.trade.order.request.orchestrator.controller

import com.fasterxml.jackson.databind.ObjectMapper
import org.fc.trade.order.request.orchestrator.model.TradeOrderRequest
import org.fc.trade.order.request.orchestrator.model.WorkItemRequest
import org.fc.trade.order.request.orchestrator.model.WorkItemResponse
import org.fc.trade.order.request.orchestrator.repository.AccountRepositoryImpl
import org.fc.trade.order.request.orchestrator.repository.ClientInvolvedPartiesJpaRepository
import org.fc.trade.order.request.orchestrator.repository.WorkFlowManagementRepositoryImpl
import org.fc.trade.order.request.orchestrator.service.EmailSenderService
import org.fc.trade.order.request.orchestrator.service.TradeOrderProcessService
import org.fc.trade.order.request.orchestrator.service.TradeOrderProcessServiceImpl
import org.junit.runner.RunWith
import org.mockito.Mockito
import org.springframework.beans.factory.annotation.Autowired
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc
import org.springframework.boot.test.context.SpringBootTest
import org.springframework.boot.test.mock.mockito.MockitoBean   // <— Spring Boot 3.4+
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

// If you used custom profiles earlier, keep them:
@ActiveProfiles(["test"])
@RunWith(SpringRunner)
@SpringBootTest
@AutoConfigureMockMvc
class TradeOrderProcessControllerIntegrationSpec extends Specification {

    @Autowired
    WebApplicationContext webApplicationContext

    @Autowired
    MockMvc mockMvc

    ObjectMapper mapper
    List<GrantedAuthority> authorities = []

    // --------- Context beans mocked at the Spring level ---------

    // Controller depends on service:
    @MockitoBean
    TradeOrderProcessService tradeOrderProcessService

    // Controller calls this after processing:
    @MockitoBean
    EmailSenderService emailService

    // ---- Legacy big/integration-ish collaborators (kept for the large test) ----
    // These three were in your screenshots; they’re only used in the legacy test
    @MockitoBean
    WorkFlowManagementRepositoryImpl workFlowManagementRepository

    @MockitoBean
    ClientInvolvedPartiesJpaRepository clientInvolvedPartiesJpaRepository

    @MockitoBean
    AccountRepositoryImpl accountRepository

    def setup() {
        mapper = new ObjectMapper()
        SecurityContextHolder.clearContext()
        // In case AutoConfigureMockMvc didn’t wire, uncomment next line:
        // mockMvc = MockMvcBuilders.webAppContextSetup(webApplicationContext).build()
    }

    // =========================================================================
    // 1) NEW focused controller tests for email behavior (keep these)
    // =========================================================================

    def "returns 200 and sends email when workItem is present"() {
        given:
        long tradeId = 12345L
        TradeOrderRequest req = GroovyUtils.getTradeOrderRequest()
        def workItemId = "W123456"
        Mockito.when(tradeOrderProcessService.processTradeOrder(any(TradeOrderRequest), eq(tradeId)))
               .thenReturn(new WorkItemResponse(workItemId, "SUCCESS"))

        when:
        def result = makeTradeOrderRequest(req, tradeId)

        then:
        result.response.status == HttpStatus.OK.value()
        // email invoked once; body contains Trade Request Id and work item id
        Mockito.verify(emailService, Mockito.times(1))
               .sendReport(argThat { String s ->
                   s.contains("Trade Request Id: " + tradeId) && s.contains(workItemId)
               })
    }

    def "returns 200 and sends email when workItem is null"() {
        given:
        long tradeId = 12345L
        TradeOrderRequest req = GroovyUtils.getTradeOrderRequest()
        Mockito.when(tradeOrderProcessService.processTradeOrder(any(TradeOrderRequest), eq(tradeId)))
               .thenReturn(new WorkItemResponse(null, "SUCCESS"))

        when:
        def result = makeTradeOrderRequest(req, tradeId)

        then:
        result.response.status == HttpStatus.OK.value()
        Mockito.verify(emailService, Mockito.times(1))
               .sendReport(argThat { String s -> s.contains("Trade Request Id: " + tradeId) })
    }

    def "sends email even if service throws and still returns 200 with empty body"() {
        given:
        long tradeId = 12345L
        TradeOrderRequest req = GroovyUtils.getTradeOrderRequest()
        Mockito.when(tradeOrderProcessService.processTradeOrder(any(TradeOrderRequest), eq(tradeId)))
               .thenThrow(new RuntimeException("boom"))

        when:
        def result = makeTradeOrderRequest(req, tradeId)

        then:
        result.response.status == HttpStatus.OK.value()
        result.response.contentAsString == "" || result.response.contentAsString == "null"
        Mockito.verify(emailService, Mockito.times(1))
               .sendReport(argThat { String s -> s.contains("Trade Request Id: " + tradeId) })
    }

    // =========================================================================
    // 2) KEEP: your validation/auth tests (slimmed but same behavior)
    // =========================================================================

    def "invalid TradeOrderRequest returns 400"() {
        given:
        long tradeId = 12345L
        TradeOrderRequest req = GroovyUtils.getTradeOrderRequest()
        req.accountNumber = accountNumber as Long
        req.brokerageAccountNumber = brokerageAccountNumber
        req.trades.get(0).symbol = symbol
        req.trades.get(0).cusip = cusip

        when:
        def result = makeTradeOrderRequest(req, tradeId)

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

    def "access is denied for authentication other than oauth"() {
        given:
        long tradeId = 12345L
        TradeOrderRequest req = GroovyUtils.getTradeOrderRequest()
        // set up your non-oauth auth in the context as you already do
        def auth = new FCUserAuthentication(userId: "1234", details: "NCEWP", authorities: authorities)
        SecurityContextHolder.getContext().setAuthentication(auth)

        when:
        def result = makeTradeOrderRequest(req, tradeId)

        then:
        result.response != null
        result.response.status == HttpStatus.FORBIDDEN.value()
        Mockito.verify(emailService, Mockito.never()).sendReport(anyString())
    }

    // =========================================================================
    // 3) KEEP: Legacy big success test (end-to-end-ish) for regression safety
    //    Mirrors your old long test but routes through the service impl + repos.
    // =========================================================================

    def "legacy: TradeOrderRequest returns 200 with full orchestration and body contains workItemId"() {
        given:
        long tradeId = 12345L
        TradeOrderRequest request = GroovyUtils.getTradeOrderRequest()

        // Mock downstream repos like in your screenshots
        def accountDetails = new GivingAccountDetailsResponse()
        def user = new User()
        user.setFirstName("Bo"); user.setLastName("Nix")
        accountDetails.setAccountNbr(1011)
        accountDetails.setAccountHolder(user)
        Mockito.when(accountRepository.getGivingAccountDetails(any(Long.class))).thenReturn(accountDetails)

        def workItemId = "W123456"
        Mockito.when(workFlowManagementRepository.createWorkItem(any(WorkItemRequest.class)))
               .thenReturn(new WorkItemResponse(workItemId, "SUCCESS"))

        Mockito.when(clientInvolvedPartiesJpaRepository.findByClientInvolvedPartyId(any(Long.class)))
               .thenReturn(Optional.of(new ClientInvolvedPartiesCGF(firstName: "Bruce", lastName: "Wayne")))

        // Wire the real service impl against mocked repos,
        // then let controller call it through the interface:
        def realService = new TradeOrderProcessServiceImpl(
                workFlowManagementRepository,
                clientInvolvedPartiesJpaRepository,
                accountRepository
        )
        Mockito.when(tradeOrderProcessService.processTradeOrder(any(TradeOrderRequest), eq(tradeId)))
               .thenAnswer { inv -> realService.processTradeOrder(inv.getArgument(0), tradeId) }

        when:
        def result = makeTradeOrderRequest(request, tradeId)

        then:
        result.response.status == HttpStatus.OK.value()
        def body = mapper.readValue(result.response.getContentAsString(), WorkItemResponse)
        body.workItemId == workItemId

        // email still sent by controller with the id included
        Mockito.verify(emailService, Mockito.times(1))
               .sendReport(argThat { String s ->
                   s.contains("Trade Request Id: " + tradeId) && s.contains(workItemId)
               })
    }

    // =========================================================================
    // helpers
    // =========================================================================

    private MvcResult makeTradeOrderRequest(TradeOrderRequest request, long tradeId) {
        String uri = "/tradeOrder/${tradeId}/execute"
        return mockMvc.perform(
                post(uri)
                        .content(mapper.writeValueAsString(request))
                        .contentType(MediaType.APPLICATION_JSON)
                        .accept(MediaType.APPLICATION_JSON)
        ).andDo(print())
         .andReturn()
    }
}
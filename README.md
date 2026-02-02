-----_------

@Service
public class TradeOrderSummaryService {

    @Autowired
    private TradeOrderRequestsCGFJpaRepository repository;

    public TradeOrderSummaryResponse getSummary() {

        // =========================
        // UNAPPROVED (Needs Action)
        // =========================
        List<String> unApprovedRequestStatuses = List.of(
                TradeOrderRequestStatusEnum.SUBMITTED.name(),
                TradeOrderRequestStatusEnum.IN_REVIEW.name(),
                TradeOrderRequestStatusEnum.PROCESSING.name()
        );

        List<String> unApprovedItemStatuses = List.of(
                TradeOrderItemStatusEnum.SUBMITTED.getStatusCode(),
                TradeOrderItemStatusEnum.FAILED.getStatusCode()
        );

        Long unApprovedCount = repository.getUnApprovedCount(
                unApprovedRequestStatuses,
                unApprovedItemStatuses
        );

        // =================
        // UNSETTLED
        // =================
        List<String> unsettledRequestStatuses = List.of(
                TradeOrderRequestStatusEnum.SUBMITTED.name(),
                TradeOrderRequestStatusEnum.IN_REVIEW.name(),
                TradeOrderRequestStatusEnum.PROCESSING.name()
        );

        List<String> unsettledItemStatuses = List.of(
                TradeOrderItemStatusEnum.APPROVED.getStatusCode(),
                TradeOrderItemStatusEnum.PENDING.getStatusCode(),
                TradeOrderItemStatusEnum.EXECUTED.getStatusCode(),
                TradeOrderItemStatusEnum.CANCELED_DONOR.getStatusCode(),
                TradeOrderItemStatusEnum.CANCELED_INTERNAL.getStatusCode(),
                TradeOrderItemStatusEnum.DENIED_NIGO.getStatusCode(),
                TradeOrderItemStatusEnum.DENIED_POLICY.getStatusCode()
        );

        Long unsettledCount = repository.getUnsettledCount(
                unsettledRequestStatuses,
                unsettledItemStatuses
        );

        // =========================
        // RESPONSE MAPPING
        // =========================
        TradeOrderSummaryResponse response = new TradeOrderSummaryResponse();
        response.setUnApprovedCount(unApprovedCount != null ? unApprovedCount.intValue() : 0);
        response.setUnsettledCount(unsettledCount != null ? unsettledCount.intValue() : 0);
        response.setCurrentSystemDateTime(ZonedDateTime.now());

        return response;
    }
}
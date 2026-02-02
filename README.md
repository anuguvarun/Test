I have seen video,we have use enum based on that we have to take value 

@Service
public class TradeOrderSummaryService {

    @Autowired
    private TradeOrderRequestsCGFJpaRepository repository;

    public TradeOrderSummaryResponse getSummary() {

        // ✅ Take item statuses from enum
        List<String> unApprovedItemStatuses =
                AdminQueueEnum.UNAPPROVED.getItemStatuses();

        List<String> unsettledItemStatuses =
                AdminQueueEnum.UNSETTLED.getItemStatuses();

        Long unApprovedCount =
                repository.getUnApprovedCount(unApprovedItemStatuses);

        Long unsettledCount =
                repository.getUnsettledCount(unsettledItemStatuses);

        TradeOrderSummaryResponse response = new TradeOrderSummaryResponse();
        response.setUnApprovedCount(unApprovedCount.intValue());
        response.setUnsettledCount(unsettledCount.intValue());
        response.setCurrentSystemDateTime(ZonedDateTime.now());

        return response;
    }
}
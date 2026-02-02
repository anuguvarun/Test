@Query(
  value = """
    SELECT COUNT(DISTINCT tor.trade_order_request_id)
    FROM trade_order_requests tor
    JOIN trade_order_request_items tori
      ON tor.trade_order_request_id = tori.trade_order_request_id
    WHERE tor.trade_order_status_code IN (:requestStatuses)
      AND tori.current_status_code IN (:itemStatuses)
  """,
  nativeQuery = true
)
Long getUnsettledCount(
    @Param("requestStatuses") List<String> requestStatuses,
    @Param("itemStatuses") List<String> itemStatuses
);
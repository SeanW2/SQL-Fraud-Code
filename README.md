# SQL-Fraud-Code
Project used in Metabase at Kikoff

SELECT date_trunc({{period}}, convert_timezone('UTC', 'America/Los_Angeles', updated_at::date))::date as "Confirmed Period",
count(distinct case 
    when BODY_TEXT LIKE '%Based on the information included in your fraud claim we have determined there is clear and sufficient evidence to remove this tradeline from your account history%'
    then ticket_id end) as "Count of Claims With Sufficient Evidence to Remove Tradeline",
count(distinct case 
    when BODY_TEXT LIKE '%Unfortunately based on this latest internal investigation of the account verification and approval process, there is insufficient evidence to support a fraudulent claim or removal of this tradeline.%'
    then ticket_id end) as "Count of Claims With Insufficient Fraud Evidence",
count(distinct case 
    when BODY_TEXT LIKE '%We are following up to inform you this ticket has been closed due to no response. Please note, we take fraud claims seriously.%'
    then ticket_id end) as "Count of Claims Closed due to No Reponse",
count(distinct case 
    when BODY_TEXT LIKE '%Additionally, we see that you are messaging us%'
    then ticket_id end) as "Count of Claims Where User Messaged From Widget"
FROM Freshdesk.conversation
where updated_at between {{start_date}} and {{end_date}}
GROUP BY 1
order by 1

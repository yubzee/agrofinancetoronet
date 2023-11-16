Application Name: AgroFinance
Overview: AgroFinance is a digital platform that connects farmers, warehouses, financial institutions,commodities inspectors, insurance companies and the government to streamline and secure the process of warehouse receipt financing. The application leverages blockchain technology for transparency, security, and efficiency.

Stakeholders Involved
1.	Farmers: The primary beneficiaries who use their commodities as collateral to secure a loan.
2.	Warehouse Owners/Operators: Responsible for storing and safeguarding the commodities in the warehouse.
3.	Financial Institutions: Provide the loans to farmers based on the value of the stored commodities.
4.	Government Agencies: Regulate and oversee warehouse receipt financing to ensure fair practices and protect the interests of all parties involved.
5.	Commodity Inspectors: Verify the quantity and quality of stored commodities, providing assurance to lenders and borrowers.
6.	Insurance Companies: Offer insurance coverage for stored commodities against risks like theft, fire, or natural disasters.
7.	Collateral Managers: Monitor and manage the collateral (commodities) on behalf of the lenders to mitigate risks.
8.	Market Intermediaries: Facilitate the buying and selling of warehouse receipts, providing liquidity to the market.
9.	Legal Advisors: Ensure that all contracts and agreements comply with relevant laws and regulations.


Key Features:
1.	User Registration:
•	Farmers, warehouse owners, financial institutions, and commodities inspectors can register on the platform.
•	Admin approval is required for farmer registration to ensure data accuracy.
2.	Warehouse Registration:
•	Warehouse owners can register their warehouses on the platform.
•	Admin approval is required for warehouse registration to ensure compliance with standards.
3.	Financial Institution Registration:
•	Financial institutions can register on the platform to participate in providing loans.
•	Admin approval is required for financial institution registration to maintain a trustworthy network.
4.	Commodities Inspector Registration:
•	Commodities inspectors can register to validate the quality and quantity of stored commodities.
•	Admin approval is required for commodities inspector registration to ensure expertise.
5.	Loan Request Process:
•	Farmers can deposit commodities into registered warehouses and request a loan.
•	Farmers receive a digital warehouse receipt upon successful deposit, serving as collateral for the loan.
6.	Approval Workflow:
•	Commodities inspectors inspect the warehouse and validate the receipt.
•	Farmers and inspectors' approvals trigger a request to the financial institution for loan approval.
7.	Loan Approval and Terms:
•	Financial institutions review the validated warehouse receipt and approve loans based on predefined criteria.
•	Loans may cover a percentage (e.g., 75%) of the commodity value with an associated interest rate and repayment date.
8.	Repayment and Release:
•	Farmers repay the loan within the specified period.
•	Upon repayment, the financial institution releases the collateral, and the farmer regains ownership of the commodities.
9.	Default Handling:
•	If a farmer defaults on a loan, the financial institution takes ownership of the stored commodities, ensuring fair risk mitigation.
10.	Insurance Integration:
•	The application integrates with insurance companies to offer coverage for stored commodities against risks like theft, fire, or natural disasters.
•	Insurance claim history is accessible for quick processing in case of an incident.
11.	Transaction History and Reporting:
•	Users can access a transparent and immutable transaction history on the blockchain.
•	Reporting tools provide insights into loan performance, warehouse utilization, and overall system efficiency.
Benefits:
•	Efficiency: Streamlines the entire warehouse receipt financing process, reducing paperwork and processing time.
•	Transparency: Utilizes blockchain for transparent and immutable transaction records, enhancing trust among stakeholders.
•	Risk Mitigation: Implement robust approval workflows and default handling mechanisms to mitigate risks for financial institutions.
•	Financial Inclusion: Enables farmers to access financing based on the value of their stored commodities, promoting financial inclusion in agriculture.
•	Quality Assurance: Commodities inspectors ensure the quality and quantity of stored commodities, reducing the risk of fraudulent activities.
Future Enhancements:
•	Mobile App: Develop a mobile application for easy access and on-the-go transactions.
•	Smart Contracts: Explore further automation using smart contracts for specific processes, reducing the need for manual intervention.
•	Integration with Supply Chain: Extend the platform to integrate with the broader agricultural supply chain for end-to-end visibility.


Smart Contract Codes

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/access/Ownable.sol";

contract WarehouseReceipt is Ownable {
    address public admin;
    uint256 public nextReceiptId;
    
    enum ApprovalStatus { Pending, Approved, Rejected }

    struct Warehouse {
        address owner;
        string wareHouseName;
        string wareHouseRegion;
        string wareHouseAddress;
        string wareHousePhone;
        bool isRegistered;
    }

    struct FinancialInstitution {
        address owner;
        string bankName;
        bool isRegistered;
    }

    struct InsuranceCompany {
        address owner;
        string insuranceName;
        bool isRegistered;
    }

    struct CommoditiesInspector {
        address owner;
        string commodityInspectorName;
        bool isRegistered;
    }

    struct Farmer {
        string farmerName;
        string farmerAddress;
        string farmerPhone;
        string farmerBVN;
        bool isRegistered;
        bool isApproved;
        uint256 balance; // for storage fee payment
    }

    struct Receipt {
        uint256 quantity;
        uint256 storageFee;
        uint256 loanAmount;
        uint256 interestRate;
        uint256 repaymentDate;
        address farmer;
        address warehouse;
        address financialInstitution;
        address inspector;
        ApprovalStatus farmerApprovalStatus;
        ApprovalStatus inspectorApprovalStatus;
        ApprovalStatus financialInstitutionApprovalStatus;
    }

    mapping(address => Warehouse) public warehouses;
    mapping(address => FinancialInstitution) public financialInstitutions;
    mapping(address => InsuranceCompany) public insuranceCompanies;
    mapping(address => CommoditiesInspector) public commoditiesInspectors;
    mapping(address => Farmer) public farmers;
    mapping(uint256 => Receipt) public receipts;

    event WarehouseRegistered(address indexed warehouseOwner);
    event FinancialInstitutionRegistered(address indexed fiOwner);
    event InsuranceCompanyRegistered(address indexed icOwner);
    event CommoditiesInspectorRegistered(address indexed ciOwner);
    event FarmerRegistered(address indexed farmer);
    event FarmerApproval(address indexed farmer, bool approved);
    event Deposit(address indexed farmer, uint256 indexed receiptId, uint256 quantity, uint256 storageFee);
    event LoanApproval(uint256 indexed receiptId, uint256 loanAmount, uint256 interestRate, uint256 repaymentDate);
    event LoanRepayment(uint256 indexed receiptId);
    event DefaultedLoan(uint256 indexed receiptId);
    event InsuranceClaimProcessed(uint256 indexed receiptId, bool approved);

    modifier onlyAdmin() {
        require(msg.sender == admin || msg.sender == owner(), "Only the admin can call this function");
        _;
    }

    modifier onlyRegisteredWarehouse() {
        require(warehouses[msg.sender].isRegistered, "Warehouse is not registered");
        _;
    }

    modifier onlyRegisteredFinancialInstitution() {
        require(financialInstitutions[msg.sender].isRegistered, "Financial institution is not registered");
        _;
    }

    modifier onlyRegisteredCommoditiesInspector() {
        require(commoditiesInspectors[msg.sender].isRegistered, "Commodities inspector is not registered");
        _;
    }

  modifier onlyRegisteredInsuranceCompany() {
        require(insuranceCompanies[msg.sender].isRegistered, "Insurance company is not registered");
        _;
    }

    modifier onlyRegisteredFarmer() {
        require(farmers[msg.sender].isRegistered, "Farmer is not registered");
        _;
    }

    modifier onlyApprovedFarmer() {
        require(farmers[msg.sender].isApproved, "Farmer registration is not approved");
        _;
    }

    modifier onlyPendingApproval(address _user) {
        require(!farmers[_user].isApproved, "User is already approved");
        _;
    }

    constructor() Ownable() {
        admin = msg.sender;
        nextReceiptId = 1;
    }

    // Function to register a warehouse by the admin
    function registerWarehouse(address _warehouseOwner, string memory _warehouseName,string memory _warehouseRegion, string memory _warehouseAddress, string memory _warehousePhone) external onlyAdmin {
        require(!warehouses[_warehouseOwner].isRegistered, "Warehouse already registered");
        warehouses[_warehouseOwner] = Warehouse(_warehouseOwner,_warehouseName,_warehouseRegion,_warehouseAddress,_warehousePhone, true);
        emit WarehouseRegistered(_warehouseOwner);
    }

    // Function to register a financial institution by the admin
    function registerFinancialInstitution(address _bankAddress, string memory _bankName) external onlyAdmin {
        require(!financialInstitutions[_bankAddress].isRegistered, "Financial institution already registered");
        financialInstitutions[_bankAddress] = FinancialInstitution(_bankAddress, _bankName,true);
        emit FinancialInstitutionRegistered(_bankAddress);
    }

    // Function to register an insurance company by the admin
    function registerInsuranceCompany(address _insuranceAddress, string memory _insuranceName) external onlyAdmin {
        require(!insuranceCompanies[_insuranceAddress].isRegistered, "Insurance company already registered");
        insuranceCompanies[_insuranceAddress] = InsuranceCompany(_insuranceAddress,_insuranceName, true);
        emit InsuranceCompanyRegistered(_insuranceAddress);
    }

    // Function to register a commodities inspector by the admin
    function registerCommoditiesInspector(address _inspectorAddress, string memory _inspectorName) external onlyAdmin {
        require(!commoditiesInspectors[_inspectorAddress].isRegistered, "Commodities inspector already registered");
        commoditiesInspectors[_inspectorAddress] = CommoditiesInspector(_inspectorAddress,_inspectorName, true);
        emit CommoditiesInspectorRegistered(_inspectorAddress);
    }

    // Function for a farmer to register
    function registerFarmer(string memory _farmerName,string memory _farmerAddress,string memory _farmerPhone,string memory _farmerBVN) external {
        require(!farmers[msg.sender].isRegistered, "Farmer already registered");
        farmers[msg.sender] = Farmer(_farmerName,_farmerAddress,_farmerPhone,_farmerBVN, true, false, 0);
        emit FarmerRegistered(msg.sender);
    }

    // Function for the admin to approve farmer registration
    function approveFarmerRegistration(address _farmer) external onlyAdmin onlyPendingApproval(_farmer) {
        farmers[_farmer].isApproved = true;
        emit FarmerApproval(_farmer, true);
    }

    // Function for a farmer to deposit commodities into the warehouse and pay storage fee
    function depositCommodities(uint256 _quantity, uint256 _storageFee) external onlyApprovedFarmer {
        require(_quantity > 0, "Quantity must be greater than 0");
        require(_storageFee > 0, "Storage fee must be greater than 0");

        // Deduct storage fee from farmer's balance
        farmers[msg.sender].balance += _storageFee;

        // Create a new receipt
        receipts[nextReceiptId] = Receipt({
            quantity: _quantity,
            storageFee: _storageFee,
            loanAmount: 0,
            interestRate: 0,
            repaymentDate: 0,
            farmer: msg.sender,
            warehouse: address(0),
            financialInstitution: address(0),
            inspector: address(0),
            farmerApprovalStatus: ApprovalStatus.Pending,
            inspectorApprovalStatus: ApprovalStatus.Pending,
            financialInstitutionApprovalStatus: ApprovalStatus.Pending
        });

        emit Deposit(msg.sender, nextReceiptId, _quantity, _storageFee);
        nextReceiptId++;
    }

    // Function for a farmer to get a copy of the warehouse receipt
    function getWarehouseReceipt(uint256 _receiptId) external onlyApprovedFarmer view returns (Receipt memory) {
        return receipts[_receiptId];
    }

    // Function for a farmer to request a loan from a financial institution using the warehouse receipt
    function requestLoan(uint256 _receiptId, address _financialInstitution, uint256 _loanAmount, uint256 _interestRate, uint256 _repaymentDate) 
        external onlyApprovedFarmer {

        require(receipts[_receiptId].farmer == msg.sender, "You don't own this receipt");
        require(receipts[_receiptId].farmerApprovalStatus == ApprovalStatus.Approved, "Receipt not approved by farmer");
        require(receipts[_receiptId].inspectorApprovalStatus == ApprovalStatus.Approved, "Receipt not approved by inspector");

        // Set the details of the loan request in the receipt
        receipts[_receiptId].financialInstitution = _financialInstitution;
        receipts[_receiptId].loanAmount = _loanAmount;
        receipts[_receiptId].interestRate = _interestRate;
        receipts[_receiptId].repaymentDate = _repaymentDate;

        emit LoanApproval(_receiptId, _loanAmount, _interestRate, _repaymentDate);
    }

    // Function for a commodities inspector to inspect the warehouse and approve the receipt
    function inspectWarehouse(uint256 _receiptId) external onlyRegisteredCommoditiesInspector {
        require(receipts[_receiptId].inspectorApprovalStatus == ApprovalStatus.Pending, "Inspector has already approved/rejected");

        // You may add more complex logic for inspection here, for simplicity, assuming all inspections are approved
        receipts[_receiptId].inspectorApprovalStatus = ApprovalStatus.Approved;
    }

    // Function for a financial institution to validate the warehouse receipt and approve the loan
    function validateWarehouseReceipt(uint256 _receiptId) external onlyRegisteredFinancialInstitution {
        require(receipts[_receiptId].financialInstitutionApprovalStatus == ApprovalStatus.Pending, "Financial institution has already approved/rejected");

        // You may add more complex validation logic here, for simplicity, assuming all validations are approved
        receipts[_receiptId].financialInstitutionApprovalStatus = ApprovalStatus.Approved;
    }

    // Function for a farmer to repay the loan
    function repayLoan(uint256 _receiptId) external onlyApprovedFarmer {
        require(receipts[_receiptId].repaymentDate > 0, "Loan not approved");
        require(block.timestamp <= receipts[_receiptId].repaymentDate, "Loan repayment date has passed");

        // Transfer the loan amount from the farmer to the financial institution
        require(farmers[msg.sender].balance >= receipts[_receiptId].loanAmount, "Insufficient balance for repayment");
        farmers[msg.sender].balance -= receipts[_receiptId].loanAmount;

        // Reset loan details in the receipt
        receipts[_receiptId].loanAmount = 0;
        receipts[_receiptId].interestRate = 0;
        receipts[_receiptId].repaymentDate = 0;

        emit LoanRepayment(_receiptId);
    }

    // Function for the admin to declare a defaulted loan and transfer ownership of commodities to financial institution
    function declareDefaultedLoan(uint256 _receiptId) external onlyAdmin {
        require(receipts[_receiptId].repaymentDate > 0 && block.timestamp > receipts[_receiptId].repaymentDate, "Loan not defaulted yet");

        // Transfer ownership of commodities to the financial institution
        receipts[_receiptId].financialInstitution = address(0);
        receipts[_receiptId].farmerApprovalStatus = ApprovalStatus.Rejected;
        receipts[_receiptId].inspectorApprovalStatus = ApprovalStatus.Rejected;
        receipts[_receiptId].financialInstitutionApprovalStatus = ApprovalStatus.Rejected;

        emit DefaultedLoan(_receiptId);
    }

    // Function for an insurance company to process an insurance claim based on the history of a warehouse receipt
    function processInsuranceClaim(uint256 _receiptId, bool _approved) external onlyRegisteredInsuranceCompany {
        require(_approved || block.timestamp > receipts[_receiptId].repaymentDate, "Claim can only be processed after repayment date");

        emit InsuranceClaimProcessed(_receiptId, _approved);
    }
}



NETWORK – TESTNET
CONTRACT ADDRESS - 0x8017EBAbcB8746291De3076618741a2718D09489
TRANSACTIONHASH 1 - 0x9ce0dc0fa0598cd085611182e3743501f5f9fa8256130adadf93e620734ae98c
TRANSACTIONHASH 2 - 0xad747e63fb1fc3645792087dddc1b59d3ffe29c6677fbdd47a427eb9faaa2086

ORIGINALITY – This is the original work by our team. We build on top of the REMIX IDE
-DEMO Link –  http://www.toronet.plutonics.online/index.html
-Video Link –  https://youtu.be/PgoPWwCSg44
-Tweet Link -  https://x.com/yubzeela/status/1724811823931596878?s=20

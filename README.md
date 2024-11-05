// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract StudentData {
    
    // Define a structure to store student details
    struct Student {
        uint id;
        string name;
        uint age;
        uint grade;
    }

    // Array to store multiple students
    Student[] public students;

    // Mapping to keep track of existing student IDs
    mapping(uint => bool) private studentExists;

    // Event to log addition of a student
    event StudentAdded(uint id, string name, uint age, uint grade);

    // Function to add a new student
    function addStudent(uint _id, string memory _name, uint _age, uint _grade) public {
        // Check if the student ID is unique
        require(!studentExists[_id], "Student ID already exists.");
        
        // Add new student to array and mark the ID as used
        students.push(Student(_id, _name, _age, _grade));
        studentExists[_id] = true;

        // Emit event after successful addition
        emit StudentAdded(_id, _name, _age, _grade);
    }

    // Fallback function to handle any unknown calls with data
    fallback() external payable {
        // For example, revert any unknown transactions
        revert("Invalid function call.");
    }

    // Receive function to accept Ether sent directly without data
    receive() external payable {
        // Accepts plain Ether transfers
    }

    // Retrieve the number of students in the contract
    function getStudentCount() public view returns (uint) {
        return students.length;
    }

    // Retrieve a specific student by index
    function getStudent(uint index) public view returns (uint, string memory, uint, uint) {
        require(index < students.length, "Index out of bounds.");
        Student memory s = students[index];
        return (s.id, s.name, s.age, s.grade);
    }
}





// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract BankAccount {
    address public owner;
    uint256 private balance;

    // Declare events for Deposit and Withdrawal
    event Deposit(address indexed sender, uint256 amount);
    event Withdrawal(address indexed owner, uint256 amount);

    modifier onlyOwner() {
        require(msg.sender == owner, "Only the owner can perform this action.");
        _;
    }

    constructor() {
        owner = msg.sender;
    }

    function deposit() public payable {
        require(msg.value > 0, "Deposit amount must be greater than zero.");
        balance += msg.value;
        emit Deposit(msg.sender, msg.value); // Log the deposit event
    }

    function withdraw(uint256 amount) public onlyOwner {
        require(amount > 0, "Withdrawal amount must be greater than zero.");
        require(amount <= balance, "Insufficient balance.");
        balance -= amount;
        emit Withdrawal(owner, amount); // Log the withdrawal event
        payable(owner).transfer(amount);
    }

    function getBalance() public view returns (uint256) {
        return balance;
    }
}

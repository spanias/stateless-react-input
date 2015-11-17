#Description
This module provides an infrastructure for managing react-bootstrap inputs. 

It provides a TextInputStore which can be used to retrieve data.
It provides TextInputActions which can be used to update and reset input data.


#Developing

Edit src folder only which is in ES6 and before deployment run 
    
    npm run-script build
    
to transcribe from ES6 to ES5 using babel. The transcribed code is placed in lib folder which is what is retrieved by npm install.

#Usage
    
    npm install ../stateless-react-input/
	

Register the TextInputStore in your app:
	
	import {TextInputStore} from 'stateless-react-input';
	let app = new Fluxible({
	    stores: [
			TextInputStore
		]	
	});

In your component import the required elements and the store:

	import {FirstNameInput,LastNameInput,EmailInput,TextInputStore} from 'stateless-react-input';	

Name your fields:

	var emailFieldName =  "AuthenticationEmailInput";
	var firstNameFieldName =  "AuthenticationFirstNameInput";
	var lastNameFieldName =  "AuthenticationLastNameInput";

Connect your component to the TextInputStore

	AuthenticationUserDetailsView = connectToStores(AuthenticationUserDetailsView,
	    [TextInputStore],
	    function (context, props) {
	        return {
	            TextInputStore: context.getStore(TextInputStore).getState()
	        };
	    });

Use the components in your render function:

                    <Row>
                        <Col xs={6}>
                            <FirstNameInput
                                fieldName={firstNameFieldName}
                                initialValue={this.props.firstName}
                                validateOnBlur = {true}
                                />
                        </Col>
                        <Col xs={6}>
                            <LastNameInput
                                fieldName={lastNameFieldName}
                                initialValue={this.props.lastName}
                                validateOnChange = {true}
                                />
                        </Col>
                    </Row>
                    <Row>
                        <Col xs={12}>
                            <EmailInput
                                fieldName={emailFieldName}
                                initialValue={this.props.email}
                                verified={this.props.verified}
                                requestValidationEmail={this._requestValidationEmail}
                                validateLazyOnChange = {true}
                                />
                        </Col>
                    </Row>

Use throughout your component:

            if (this.props.TextInputStore[firstNameFieldName] && this.props.TextInputStore[firstNameFieldName].hasChanges && this.props.TextInputStore[firstNameFieldName].isValid)
            {
                myUser.firstName = this.props.TextInputStore[firstNameFieldName].fieldValue;
            }

Or

    _hasChanges()
    {
        return ((this.props.TextInputStore[emailFieldName] && this.props.TextInputStore[emailFieldName].hasChanges) ||
        (this.props.TextInputStore[firstNameFieldName] && this.props.TextInputStore[firstNameFieldName].hasChanges) ||
        (this.props.TextInputStore[lastNameFieldName] && this.props.TextInputStore[lastNameFieldName].hasChanges) );
    }



#Customizable TextInput Component

The TextInput component is used to customize the behaviour of <Input /> in react-bootstrap automating storage of values in the store and validation.

	<TextInput>
	TextInput.propTypes = {
	    fieldType: React.PropTypes.string.isRequired,	//Field Type defines the type of data this input stores (adds more data to the store (e.g. emailType also includes a verified boolean))
	    fieldName: React.PropTypes.string.isRequired,	//Field Name is as a reference in the store
	    fieldAfter: React.PropTypes.any,				//Contains an object which overwrites what is added in react-bootstraps addonAfter prop
	    fieldResetOnUnmount: React.PropTypes.bool,		//Field Reset On Unmount - if true erases the store data of this field on unmount (recommended for passwords)
	    initialValue: React.PropTypes.string,			//Initial value, the value of the store as soon as it is mounted
	    validateOnChange: React.PropTypes.bool,			//Set this to true for validation to occur on change
	    validateLazyOnChange: React.PropTypes.bool,		//Set this to true for validation to occur on change with a slight delay only when typing stops
	    validateOnBlur: React.PropTypes.bool,			//Set this to true for validation to occur on blur
	    validationFunction: React.PropTypes.func,		//A function which returns true of false (true for valid and false for invalid)
	    onChange: React.PropTypes.func,					//Overwrites the onChange function (overwriting breaks value update of the store)
	    onBlur: React.PropTypes.func,					//Overwrites the onBlur function (overwriting breaks the optional validation)
	    onFocus: React.PropTypes.func					//Overwritings the onFocus function
	};
	

Additionally all props which can be used in <Input /> can also be used with <TextInput />

#Built In TextInput Customizations

<EmailInput> is a customized TextInput which uses TextInputActions.validateEmail function for validation and also has a preset addonAfter which displays a verified/unverified label based on  this.props.verified. Also sets the default label to "E-Mail Address" and the default placeholder to "someone@somewhere.com".
	
    <TextInput
        {...this.props}
        fieldType="emailInput"
        fieldAfter={verifiedLabel}
        placeholder = {placeholder}
        label = {label}
        validationFunction = {validationFunction}
        />;
	
		EmailInput.propTypes = {
		    fieldName: React.PropTypes.string.isRequired,  	//Field Name is as a reference in the store
		    requestValidationEmail: React.PropTypes.func	//This is the function that is activated when the unverified button on the after side is pressed
		};
		

<FirstNameInput> is a simpler variation of TextInput. Uses default validation function TextInputActions.validateFirstName and sets some default values for label and placeholder.
	
    <TextInput
        {...this.props}
        fieldType="firstNameInput"
        fieldAfter={addonAfter}
        placeholder = {placeholder}
        label = {label}
        validationFunction = {validationFunction}
        />;

<LastNameInput> uses TextInputActions.validateLastName for validation and provides default values for label and placeholder.
	
    <TextInput
        {...this.props}
        fieldType="lastNameInput"
        fieldAfter={addonAfter}
        placeholder = {placeholder}
        label = {label}
        validationFunction = {validationFunction}
        />;

<UsernameInput> uses TextInputActions.validateUsername for validation and has default values for label and placeholder
	
    <TextInput
        {...this.props}
        fieldType="usernameInput"
        fieldAfter={addonAfter}
        placeholder = {placeholder}
        label = {label}
        validationFunction = {validationFunction}
        />;

<PasswordInput> uses TextInputActions.validatePassword and has default values forlabel and placeholder. Also by setting the fieldType to passwordInput TextInput automatically changes the <Input type="password">

    <TextInput
        {...this.props}
        fieldType="passwordInput"
        fieldAfter={addonAfter}
        fieldResetOnUnmount= {fieldResetOnUnmount}
        placeholder = {placeholder}
        label = {label}
        validationFunction = {validationFunction}
        />;

<NewPasswordInput> uses two <PasswordInput> components and compares them for matching passwords. The isValid prop is used to validate directly from the parent component.
	
    _confirmPasswordFieldValidation(value){
        if (this.props.TextInputStore[this.props.newPasswordFieldName] &&
            this.props.TextInputStore[this.props.newPasswordFieldName].fieldValue == value){
            return true;
        }
        else {
            return false;
        }
    }

It's render looks like this:

				<Row>
	                <Col xs={6}>
	                    <PasswordInput
	                        fieldName={this.props.newPasswordFieldName}
	                        initialValue=""
	                        placeholder="New Password"
	                        label="New password"
	                        validateOnChange = {true} />
	                </Col>
	                <Col xs={6}>
	                    <PasswordInput
	                        fieldName={this.props.confirmPasswordFieldName}
	                        initialValue=""
	                        placeholder="Confirm password"
	                        label="Confirm password"
	                        isValid={(this.props.TextInputStore[this.props.newPasswordFieldName] &&
	                                    this.props.TextInputStore[this.props.newPasswordFieldName].isValid &&
	                                    this.props.TextInputStore[this.props.confirmPasswordFieldName] &&
	                                    this.props.TextInputStore[this.props.newPasswordFieldName].fieldValue == this.props.TextInputStore[this.props.confirmPasswordFieldName].fieldValue)}
	                        validationFunction = {this._confirmPasswordFieldValidation}
	                        validateOnChange = {true} />
	                </Col>
	            </Row>
				

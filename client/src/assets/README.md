#### Setup

- create new folder (back-end)
- create package.json

```sh
npm init -y
```

- create README.md and copy values from final
- create and test server.js

```sh
node server
```

#### ES6 Modules

package.json

```json
  "type": "module",
```

Create test.js and implement named import

test.js

```js
export const value = 42;
```

server.js

```js
import { value } from './test.js';
console.log(value);
```

- don't forget about .js extension
- for named imports, names must match

#### Setup Basic Express

- install express and nodemon.
- setup a basic server which listening on PORT=5100
- create a basic home route which sends back "hello world"
- setup a script with nodemon package.

```sh
npm i express nodemon
```

server.js

```js
import express from 'express';
const app = express();

app.get('/', (req, res) => {
  res.send('Hello World');
});

app.listen(5100, () => {
  console.log('server running....');
});
```

package.json

```json
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "node server.js",
    "dev": "nodemon server.js"
  },
```

#### Thunder Client

- install and test home route

#### Accept JSON

Setup express middleware to accept json

server

```js
app.use(express.json());

app.post('/', (req, res) => {
  console.log(req);

  res.json({ message: 'Data received', data: req.body });
});
```

#### Morgan and Dotenv

[Morgan](https://www.npmjs.com/package/morgan)

HTTP request logger middleware for node.js

[Dotenv](https://www.npmjs.com/package/dotenv)

Dotenv is a zero-dependency module that loads environment variables from a .env file into process.env.

```sh
npm i morgan dotenv
```

```js
import morgan from 'morgan';

app.use(morgan('dev'));
```

- create .env file in the root
- add PORT and NODE_ENV
- setup .gitignore

server.js

```js
import * as dotenv from 'dotenv';
dotenv.config();

if (process.env.NODE_ENV === 'development') {
  app.use(morgan('dev'));
}

const port = process.env.PORT || 5100;
app.listen(port, () => {
  console.log(`server running on PORT ${port}....`);
});
```

#### New Features

- fetch API
- global await
- watch mode

```js
try {
  const response = await fetch(
    'https://www.course-api.com/react-useReducer-cart-project'
  );
  const cartData = await response.json();
  console.log(cartData);
} catch (error) {
  console.log(error);
}
```

package.json

```json
 "scripts": {
    "watch": "node --watch server.js "
  },
```

#### Basic CRUD

- create jobs array where each item is an object with following properties
  id, company, position
- create routes to handle - create, read, update and delete functionalities

#### Get All Jobs

[Nanoid](https://www.npmjs.com/package/nanoid)

```sh
npm i nanoid
```

server.js

```js
import { nanoid } from 'nanoid';

const jobs = [
  { id: nanoid(), company: 'apple', position: 'front-end developer' }
  { id: nanoid(), company: 'google', position: 'back-end developer' }
  ];

  app.get('/api/v1/jobs', (req, res) => {
  res.status(200).json({ jobs });
});
```

#### Create, FindOne, Modify and Delete

```js
// CREATE JOB

app.post('/api/v1/jobs', (req, res) => {
  const { company, position } = req.body;
  if (!company || !position) {
    return res.status(400).json({ msg: 'please provide company and position' });
  }
  const id = nanoid(10);
  // console.log(id);
  const job = { id, company, position };
  jobs.push(job);
  res.status(200).json({ job });
});

// GET SINGLE JOB

app.get('/api/v1/jobs/:id', (req, res) => {
  const { id } = req.params;
  const job = jobs.find((job) => job.id === id);
  if (!job) {
    return res.status(404).json({ msg: `no job with id ${id}` });
  }
  res.status(200).json({ job });
});

// EDIT JOB

app.patch('/api/v1/jobs/:id', (req, res) => {
  const { company, position } = req.body;
  if (!company || !position) {
    return res.status(400).json({ msg: 'please provide company and position' });
  }
  const { id } = req.params;
  const job = jobs.find((job) => job.id === id);
  if (!job) {
    return res.status(404).json({ msg: `no job with id ${id}` });
  }

  job.company = company;
  job.position = position;
  res.status(200).json({ msg: 'job modified', job });
});

// DELETE JOB

app.delete('/api/v1/jobs/:id', (req, res) => {
  const { id } = req.params;
  const job = jobs.find((job) => job.id === id);
  if (!job) {
    return res.status(404).json({ msg: `no job with id ${id}` });
  }
  const newJobs = jobs.filter((job) => job.id !== id);
  jobs = newJobs;

  res.status(200).json({ msg: 'job deleted' });
});
```

#### Not Found and Error

```js
app.use('*', (req, res) => {
  res.status(404).json({ msg: 'not found' });
});

app.use((err, req, res, next) => {
  console.log(err);
  res.status(500).json({ msg: 'something went wrong' });
});
```

- make a request to "/jobss"

```js
// GET ALL JOBS
app.get('/api/v1/jobs', (req, res) => {
  // console.log(jobss);
  res.status(200).json({ jobs });
});

// GET SINGLE JOB
app.get('/api/v1/jobs/:id', (req, res) => {
  const { id } = req.params;
  const job = jobs.find((job) => job.id === id);
  if (!job) {
    throw new Error('no job with that id');
    return res.status(404).json({ msg: `no job with id ${id}` });
  }
  res.status(200).json({ job });
});
```

#### Controller and Router

setup controllers and router

controllers/jobController.js

```js
import { nanoid } from 'nanoid';

let jobs = [
  { id: nanoid(), company: 'apple', position: 'front-end developer' },
  { id: nanoid(), company: 'google', position: 'back-end developer' },
];

export const getAllJobs = async (req, res) => {
  res.status(200).json({ jobs });
};

export const createJob = async (req, res) => {
  const { company, position } = req.body;

  if (!company || !position) {
    return res.status(400).json({ msg: 'please provide company and position' });
  }
  const id = nanoid(10);
  const job = { id, company, position };
  jobs.push(job);
  res.status(200).json({ job });
};

export const getJob = async (req, res) => {
  const { id } = req.params;
  const job = jobs.find((job) => job.id === id);
  if (!job) {
    // throw new Error('no job with that id');
    return res.status(404).json({ msg: `no job with id ${id}` });
  }
  res.status(200).json({ job });
};

export const updateJob = async (req, res) => {
  const { company, position } = req.body;
  if (!company || !position) {
    return res.status(400).json({ msg: 'please provide company and position' });
  }
  const { id } = req.params;
  const job = jobs.find((job) => job.id === id);
  if (!job) {
    return res.status(404).json({ msg: `no job with id ${id}` });
  }

  job.company = company;
  job.position = position;
  res.status(200).json({ msg: 'job modified', job });
};

export const deleteJob = async (req, res) => {
  const { id } = req.params;
  const job = jobs.find((job) => job.id === id);
  if (!job) {
    return res.status(404).json({ msg: `no job with id ${id}` });
  }
  const newJobs = jobs.filter((job) => job.id !== id);
  jobs = newJobs;

  res.status(200).json({ msg: 'job deleted' });
};
```

routes/jobRouter.js

```js
import { Router } from 'express';
const router = Router();

import {
  getAllJobs,
  getJob,
  createJob,
  updateJob,
  deleteJob,
} from '../controllers/job-controller.js';

// router.get('/', getAllJobs);
// router.post('/', createJob);

router.route('/').get(getAllJobs).post(createJob);
router.route('/:id').get(getJob).patch(updateJob).delete(deleteJob);

export default router;
```

server.js

```js
import jobRouter from './routers/job-router.js';
app.use('/api/v1/jobs', jobRouter);
```

#### MongoDB

[MongoDb](https://www.mongodb.com/)

#### Mongoosejs

[Mongoose](https://mongoosejs.com/)

```sh
npm i mongoose
```

server.js

```js
import mongoose from 'mongoose';

try {
  await mongoose.connect(process.env.MONGO_URL);
  app.listen(port, () => {
    console.log(`server running on PORT ${port}....`);
  });
} catch (error) {
  console.log(error);
  process.exit(1);
}
```

#### Job Model

models/Job.js

```js
import mongoose from 'mongoose';

const JobSchema = new mongoose.Schema(
  {
    company: String,
    position: String,
    status: {
      type: String,
      enum: ['interview', 'declined', 'pending'],
      default: 'pending',
    },
    jobType: {
      type: String,
      enum: ['full-time', 'part-time', 'internship'],
      default: 'full-time',
    },
    jobLocation: {
      type: String,
      default: 'my city',
    },
  },
  { timestamps: true }
);

export default mongoose.model('Job', JobSchema);
```

#### Create Job

jobController.js

```js
import Job from '../models/Job.js';

export const createJob = async (req, res) => {
  const { company, position } = req.body;
  const job = await Job.create({ company, position });
  res.status(201).json({ job });
};
```

#### Try / Catch

jobController.js

```js
export const createJob = async (req, res) => {
  try {
    const job = await Job.create(req.body);
    res.status(201).json({ job });
  } catch (error) {
    res.status(500).json({ msg: 'server error' });
  }
};
```

#### express-async-errors

[Express Async Errors](https://www.npmjs.com/package/express-async-errors)

```sh
npm i express-async-errors
```

- setup import at the top !!!

  server.js

```js
import 'express-async-errors';
```

#### Get All Jobs

jobController.js

```js
export const getAllJobs = async (req, res) => {
  const jobs = await Job.find({});
  res.status(200).json({ jobs });
};
```

#### Get Single Job

```js
export const getJob = async (req, res) => {
  const { id } = req.params;
  const job = await Job.findById(id);
  if (!job) {
    return res.status(404).json({ msg: `no job with id ${id}` });
  }
  res.status(200).json({ job });
};
```

#### Delete Job

jobController.js

```js
export const deleteJob = async (req, res) => {
  const { id } = req.params;
  const job = await Job.findByIdAndDelete(id);

  if (!job) {
    return res.status(404).json({ msg: `no job with id ${id}` });
  }
  res.status(200).json({ job });
};
```

#### Update Job

```js
export const updateJob = async (req, res) => {
  const { id } = req.params;
  const updatedJob = await Job.findByIdAndUpdate(id, req.body, {
    new: true,
    runValidators: true,
  });
  if (!updatedJob) {
    return res.status(404).json({ msg: `no job with id ${id}` });
  }
  res.status(200).json({ job: updatedJob });
};
```

#### Status Codes

[Http Status Codes](https://www.npmjs.com/package/http-status-codes)

```sh
npm i http-status-codes

```

#### Custom Error Class

customErrors.js

```js
import { StatusCodes } from 'http-status-codes';
export class NotFoundError extends Error {
  constructor(message) {
    super(message);
    this.name = 'NotFoundError';
    this.statusCode = StatusCodes.NOT_FOUND;
  }
}
```

This code defines a custom error class NotFoundError that extends the built-in Error class in JavaScript. The NotFoundError class is designed to be used when a requested resource is not found, and it includes a status code of 404 to indicate this.

Here's a breakdown of the code:

class NotFoundError extends Error: This line defines a new class NotFoundError that extends the built-in Error class. This means that NotFoundError inherits all of the properties and methods of the Error class, and can also define its own properties and methods.

constructor(message): This is the constructor method for the NotFoundError class, which is called when a new instance of the class is created. The message parameter is the error message that will be displayed when the error is thrown.

super(message): This line calls the constructor of the Error class and passes the message parameter to it. This sets the error message for the NotFoundError instance.

this.name = "NotFoundError": This line sets the name property of the NotFoundError instance to "NotFoundError". This is a built-in property of the Error class that specifies the name of the error.

this.statusCode = 404: This line sets the statusCode property of the NotFoundError instance to 404. This is a custom property that is specific to the NotFoundError class and indicates the HTTP status code that should be returned when this error occurs.

By creating a custom error class like NotFoundError, you can provide more specific error messages and properties to help with debugging and error handling in your application.

#### Custom Error

jobController.js

```js
import { NotFoundError } from '../customErrors.js';

if (!job) {
  throw new NotFoundError(`no job with id ${id}`);
}
```

middleware/errorHandlerMiddleware.js

```js
import { StatusCodes } from 'http-status-codes';
const errorHandlerMiddleware = (err, req, res, next) => {
  console.log(err);
  const statusCode = err.statusCode || StatusCodes.INTERNAL_SERVER_ERROR;
  const msg = err.message || 'Something went wrong, try again later';

  res.status(statusCode).json({ msg });
};

export default errorHandlerMiddleware;
```

server.js

```js
import errorHandlerMiddleware from './middleware/errorHandlerMiddleware.js';

app.use(errorHandlerMiddleware);
```

#### Bad Request Error

customErrors.js

```js
export class BadRequestError extends Error {
  constructor(message) {
    super(message);
    this.name = 'BadRequestError';
    this.statusCode = StatusCodes.BAD_REQUEST;
  }
}
```

#### Validation Layer

[Express Validator](https://express-validator.github.io/docs/)

```sh
npm i express-validator
```

#### Test Route

server.js

```js
app.post('/api/v1/test', (req, res) => {
  const { name } = req.body;
  res.json({ msg: `hello ${name}` });
});
```

#### Express Validator

```js
import { body, validationResult } from 'express-validator';

app.post(
  '/api/v1/test',
  [body('name').notEmpty().withMessage('name is required')],
  (req, res) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      const errorMessages = errors.array().map((error) => error.msg);
      console.log(errorMessages);
      return res.status(400).json({ errors: errorMessages });
    }
    const { name } = req.body;
    res.json({ msg: `hello ${name}` });
  }
);
```

#### Validation Middleware

```js
import { body, validationResult } from 'express-validator';

const withValidationErrors = (validateFn) => {
  return [
    validateFn,
    (req, res, next) => {
      const errors = validationResult(req);
      if (!errors.isEmpty()) {
        const errorMessages = errors.array().map((error) => error.msg);
        throw new BadRequestError(errorMessages);
      }
      next();
    },
  ];
};

const validateTest = withValidationErrors([
  body('name')
    .notEmpty()
    .withMessage('name is required')
    .isLength({ min: 3, max: 50 })
    .withMessage('name must be between 3 and 50 characters long')
    .trim(),
]);

export { validateTest };
```

#### Validate Create Job

validationMiddleware.js

```js
const validateJobInput = withValidationErrors([
  body('company').notEmpty().withMessage('company is required'),
  body('position').notEmpty().withMessage('position is required'),
  body('jobLocation').notEmpty().withMessage('job location is required'),
  body('status')
    .isIn(Object.values(JOB_STATUS))
    .withMessage('invalid status value'),
  body('jobType').isIn(Object.values(JOB_TYPE)).withMessage('invalid job type'),
]);

export { validateTest, validateJobInput };
```

```js
import { validateJobInput } from '../middleware/validationMiddleware.js';

router.route('/').get(getAllJobs).post(validateJobInput, createJob);
router
  .route('/:id')
  .get(getJob)
  .patch(validateJobInput, updateJob)
  .delete(deleteJob);
```

#### Validate ID Parameter

validationMiddleware.js

```js
const mongoose = require('mongoose');

const validateIdParam = withValidationErrors([
  param('id')
    .custom((value) => mongoose.Types.ObjectId.isValid(value))
    .withMessage('invalid MongoDB id'),
]);

export { validateTest, validateJobInput, validateIdParam };
```

#### Clean DB

#### User Model

models/User.js

```js
import mongoose from 'mongoose';

const UserSchema = new mongoose.Schema({
  name: String,
  email: String,
  password: String,
  lastName: {
    type: String,
    default: 'lastName',
  },
  location: {
    type: String,
    default: 'my city',
  },
  role: {
    type: String,
    enum: ['user', 'admin'],
    default: 'user',
  },
});

export default mongoose.model('User', UserSchema);
```

#### User Controller and Router

controllers/authController.js

```js
const register = async (req, res) => {
  res.send('register');
};
const login = async (req, res) => {
  res.send('register');
};

export { register, login };
```

routers/authRouter.js

```js
import { Router } from 'express';
import { register, login } from '../controllers/authController.js';
const router = Router();

router.post('/register', register);
router.post('/login', login);

export default router;
```

server.js

```js
import authRouter from './routers/authRouter.js';

app.use('/api/v1/jobs', jobRouter);
```

#### Create User - Initial Setup

authController.js

```js
import { StatusCodes } from 'http-status-codes';
import User from '../models/User.js';

const register = async (req, res) => {
  const { email, name, password } = req.body;
  const user = await User.create({ name, email, password });
  res.status(StatusCodes.CREATED).json({ user });
};
```

#### Admin Role

authController.js

```js
// first registered user is an admin
const isFirstAccount = (await User.countDocuments({})) === 0;
const role = isFirstAccount ? 'admin' : 'user';

const user = await User.create({ name, email, password, role });
```

#### Validate User

validationMiddleware.js

```js
const validateRegisterInput = withValidationErrors([
  body('name').notEmpty().withMessage('name is required'),
  body('email')
    .notEmpty()
    .withMessage('email is required')
    .isEmail()
    .withMessage('invalid email format')
    .custom(async (email) => {
      const user = await User.findOne({ email });
      if (user) {
        throw new Error('email already exists');
      }
      return true;
    }),
  body('password')
    .notEmpty()
    .withMessage('password is required')
    .isLength({ min: 8 })
    .withMessage('password must be at least 8 characters long'),
]);
```

authRouter.js

```js
import { validateRegisterInput } from '../middleware/validationMiddleware.js';

router.post('/register', validateRegisterInput, register);
```

#### Hash Passwords

[bcryptjs](https://www.npmjs.com/package/bcryptjs)

```sh
npm i bcryptjs

```

authController.js

```js
import bcrypt from 'bcryptjs';

const register = async (req, res) => {
  const salt = await bcrypt.genSalt(10);
  const hashedPassword = await bcrypt.hash(password, salt);
  const user = await User.create({
    name,
    email,
    password: hashedPassword,
    role,
  });
};
```

const salt = await bcrypt.genSalt(10);
This line generates a random "salt" value that will be used to hash the password. A salt is a random value that is added to the password before hashing, which helps to make the resulting hash more resistant to attacks like dictionary attacks and rainbow table attacks. The genSalt() function in bcrypt generates a random salt value using a specified "cost" value. The cost value determines how much CPU time is needed to calculate the hash, and higher cost values result in stronger hashes that are more resistant to attacks.

In this example, a cost value of 10 is used to generate the salt. This is a good default value that provides a good balance between security and performance. However, you may need to adjust the cost value based on the specific needs of your application.

const hashedPassword = await bcrypt.hash(password, salt);
This line uses the generated salt value to hash the password. The hash() function in bcrypt takes two arguments: the password to be hashed, and the salt value to use for the hash. It then calculates the hash value using a one-way hash function and the specified salt value.

The resulting hash value is a string that represents the hashed password. This string can then be stored in a database or other storage mechanism to be compared against the user's password when they log in.

By using a salt value and a one-way hash function, bcrypt helps to ensure that user passwords are stored securely and are resistant to attacks like password cracking and brute-force attacks.

##### BCRYPT VS BCRYPTJS

bcrypt and bcryptjs are both popular libraries for hashing passwords in Node.js applications. However, bcryptjs is considered to be a better choice for a few reasons:

Cross-platform compatibility: bcrypt is a native Node.js module that uses C++ bindings, which can make it difficult to install and use on some platforms. bcryptjs, on the other hand, is a pure JavaScript implementation that works on any platform.

Security: While both bcrypt and bcryptjs use the same underlying algorithm for hashing passwords, bcryptjs is designed to be more resistant to certain types of attacks, such as side-channel attacks.

Ease of use: bcryptjs has a simpler and more intuitive API than bcrypt, which can make it easier to use and integrate into your application.

Maintenance: bcrypt has not had any updates since 2017, while bcryptjs is still being actively maintained and updated.

Overall, while bcrypt and bcryptjs are both good choices for hashing passwords in Node.js applications, bcryptjs is considered to be a better choice for its cross-platform compatibility, improved security, ease of use, and ongoing maintenance.

#### Setup Password Utils

utils/passwordUtils.js

```js
import bcrypt from 'bcryptjs';

export async function hashPassword(password) {
  const salt = await bcrypt.genSalt(10);
  const hashedPassword = await bcrypt.hash(password, salt);
  return hashedPassword;
}
```

authController.js

```js
import { hashPassword } from '../utils/passwordUtils.js';

const register = async (req, res) => {
  const hashedPassword = await hashPassword(password);
};
```

#### JSON Web Token

A JSON Web Token (JWT) is a compact and secure way of transmitting data between parties. It is often used to authenticate and authorize users in web applications and APIs. JWTs contain information about the user and additional metadata, and can be used to securely transmit this information

[Useful Resource](https://jwt.io/introduction)

```sh
npm i jsonwebtoken
```

utils/tokenUtils.js

```js
import jwt from 'jsonwebtoken';

export const createJWT = (payload) => {
  const token = jwt.sign(payload, process.env.JWT_SECRET, {
    expiresIn: process.env.JWT_EXPIRES_IN,
  });
  return token;
};
```

authController.js

```js
import { createJWT } from '../utils/tokenUtils.js';

const token = createJWT({ userId: user._id, role: user.role });

res.status(StatusCodes.CREATED).json({
  user: {
    name: user.name,
    email: user.email,
    lastName: user.lastName,
    location: user.location,
    role: user.role,
  },
  // ONLY TEMPORARY !!!
  token,
});
```

#### HTTP Only Cookie

An HTTP-only cookie is a cookie that can't be accessed by JavaScript running in the browser. It is designed to help prevent cross-site scripting (XSS) attacks, which can be used to steal cookies and other sensitive information.

##### HTTP Only Cookie VS Local Storage

An HTTP-only cookie is a type of cookie that is designed to be inaccessible to JavaScript running in the browser. It is primarily used for authentication purposes and is a more secure way of storing sensitive information like user tokens. Local storage, on the other hand, is a browser-based storage mechanism that is accessible to JavaScript, and is used to store application data like preferences or user-generated content. While local storage is convenient, it is not a secure way of storing sensitive information as it can be accessed and modified by JavaScript running in the browser.

authControllers.js

```js
const oneDay = 1000 * 60 * 60 * 24;

res.cookie('token', token, {
  httpOnly: true,
  expires: new Date(Date.now() + oneDay),
  secure: process.env.NODE_ENV === 'production',
});
```

```js
const oneDay = 1000 * 60 * 60 * 24;
```

This line defines a constant oneDay that represents the number of milliseconds in a day. This value is used later to set the expiration time for the cookie.

```js
res.cookie('token', token, {...});:
```

This line sets a cookie with the name "token" and a value of token, which is the JWT that was generated for the user. The ... represents an object containing additional options for the cookie.

httpOnly: true: This option makes the cookie inaccessible to JavaScript running in the browser. This helps to prevent cross-site scripting (XSS) attacks, which can be used to steal cookies and other sensitive information.

expires: new Date(Date.now() + oneDay): This option sets the expiration time for the cookie. In this case, the cookie will expire one day from the current time (as represented by Date.now() + oneDay).

secure: process.env.NODE_ENV === 'production': This option determines whether the cookie should be marked as secure or not. If the NODE_ENV environment variable is set to "production", then the cookie is marked as secure, which means it can only be transmitted over HTTPS. This helps to prevent man-in-the-middle (MITM) attacks, which can intercept and modify cookies that are transmitted over unsecured connections.

jobsController.js

```js
export const getAllJobs = async (req, res) => {
  console.log(req);
  const jobs = await Job.find({});
  res.status(StatusCodes.OK).json({ jobs });
};
```

#### Login User

validationMiddleware.js

```js
const validateLoginInput = withValidationErrors([
  body('email')
    .notEmpty()
    .withMessage('email is required')
    .isEmail()
    .withMessage('invalid email format'),
  body('password').notEmpty().withMessage('password is required'),
]);
export { validateLoginInput };
```

authRouter.js

```js
import { validateLoginInput } from '../middleware/validationMiddleware.js';

router.post('/login', validateLoginInput, login);
```

#### Unauthenticated Error

customErrors.js

```js
export class UnauthenticatedError extends Error {
  constructor(message) {
    super(message);
    this.name = 'UnauthenticatedError';
    this.statusCode = StatusCodes.UNAUTHORIZED;
  }
}
```

authController.js

```js
const login = async (req, res) => {
  const { email, password } = req.body;

  // check if user exists
  // check if password is correct

  const user = await User.findOne({ email });

  if (!user) {
    throw new UnauthenticatedError('invalid credentials');
  }

  res.send('login route');
};
```

#### Compare Password

passwordUtils.js

```js
export async function comparePassword(password, hashedPassword) {
  const isMatch = await bcrypt.compare(password, hashedPassword);
  return isMatch;
}
```

authController.js

```js
import { hashPassword, comparePassword } from '../utils/passwordUtils.js';

const login = async (req, res) => {
  const { email, password } = req.body;

  // check if user exists
  // check if password is correct

  const user = await User.findOne({ email });

  if (!user) {
    throw new UnauthenticatedError('invalid credentials');
  }
  const isPasswordCorrect = await comparePassword(password, user.password);

  if (!isPasswordCorrect) {
    throw new UnauthenticatedError('invalid credentials');
  }
  res.send('login route');
};
```

Refactor

```js
const isValidUser = user && (await comparePassword(password, user.password));
if (!isValidUser) {
  throw new UnauthenticatedError('invalid credentials');
}
```

#### Clean DB

#### Connect User and Job

models/User.js

```js
const JobSchema = new mongoose.Schema(
  {
    ....
    createdBy: {
      type: mongoose.Types.ObjectId,
      ref: 'User',
    },
  },
  { timestamps: true }
);
```

#### Auth Middleware

middleware/authMiddleware.js

```js
export const authenticateUser = async (req, res, next) => {
  console.log('auth middleware');
  next();
};
```

server.js

```js
import { authenticateUser } from './middleware/authMiddleware.js';

app.use('/api/v1/jobs', authenticateUser, jobRouter);
```

##### Cookie Parser

[Cookie Parser](https://www.npmjs.com/package/cookie-parser)

```sh
npm i cookie-parser
```

server.js

```js
import cookieParser from 'cookie-parser';
```

#### Access Token

authMiddleware.js

```js
import { UnauthenticatedError } from '../customErrors.js';

export const authenticateUser = async (req, res, next) => {
  const { token } = req.cookies;
  if (!token) {
    throw new UnauthenticatedError('authentication invalid');
  }
  next();
};
```

#### Verify Token

utils/tokenUtils.js

```js
export const verifyJWT = (token) => {
  const decoded = jwt.verify(token, process.env.JWT_SECRET);
  return decoded;
};
```

authMiddleware.js

```js
import { UnauthenticatedError } from '../customErrors.js';
import { verifyJWT } from '../utils/tokenUtils.js';

export const authenticateUser = async (req, res, next) => {
  const { token } = req.cookies;
  if (!token) {
    throw new UnauthenticatedError('authentication invalid');
  }

  try {
    const { userId, role } = verifyJWT(token);
    req.user = { userId, role };
    next();
  } catch (error) {
    throw new UnauthenticatedError('authentication invalid');
  }
};
```

jobController.js

```js
export const getAllJobs = async (req, res) => {
  console.log(req.user);
  const jobs = await Job.find({ createdBy: req.user.userId });
  res.status(StatusCodes.OK).json({ jobs });
};
```

#### Refactor Create User

jobController.js

```js
export const createJob = async (req, res) => {
  req.body.createdBy = req.user.userId;
  const job = await Job.create(req.body);
  res.status(StatusCodes.CREATED).json({ job });
};
```

#### Unauthorized Error

customErrors.js

```js
export class UnauthorizedError extends Error {
  constructor(message) {
    super(message);
    this.name = 'UnauthorizedError';
    this.statusCode = StatusCodes.FORBIDDEN;
  }
}
```

#### Check Permissions

jobController.js

```js
export const getJob = async (req, res) => {
  const { id } = req.params;
  const job = await Job.findById(id);
  if (!job) {
    throw new NotFoundError(`no job with id ${id}`);
  }
  if (job.createdBy.toString() !== req.user.userId) {
    throw new Error('not authorized to access this job');
  }
  res.status(StatusCodes.OK).json({ job });
};
```

#### CheckPermissions Middleware

utils/checkPermissions.js

```js
import { UnauthorizedError } from '../customErrors.js';

const checkPermissions = (requestUser, resourceUserId) => {
  if (requestUser.role === 'admin') return;
  if (requestUser.userId === resourceUserId.toString()) return;
  throw new UnauthorizedError('not authorized to access this route');
};

export default checkPermissions;
```

jobController.js

```js
export const getJob = async (req, res) => {
  const { id } = req.params;
  const job = await Job.findById(id);
  if (!job) {
    throw new NotFoundError(`no job with id ${id}`);
  }
  checkPermissions(req.user, job.createdBy);

  res.status(StatusCodes.OK).json({ job });
};
```

```js
export const updateJob = async (req, res) => {
  const { id } = req.params;

  const job = await Job.findById(id);

  if (!job) {
    throw new NotFoundError(`no job with id ${id}`);
  }

  checkPermissions(req.user, job.createdBy);

  const updatedJob = await Job.findByIdAndUpdate(id, req.body, {
    new: true,
    runValidators: true,
  });

  res.status(StatusCodes.OK).json({ job: updatedJob });
};
```

```js
export const deleteJob = async (req, res) => {
  const { id } = req.params;
  const job = await Job.findById(id);

  if (!job) {
    throw new NotFoundError(`no job with id ${id}`);
  }

  checkPermissions(req.user, job.createdBy);
  const removedJob = await Job.findByIdAndDelete(id);

  res.status(StatusCodes.OK).json({ job: removedJob });
};
```

#### Front End

[Vite](https://vitejs.dev/guide/)

```sh
# npm 6.x
npm create vite@latest client --template react

# npm 7+, extra double-dash is needed:
npm create vite@latest client -- --template react
```

#### Client Setup and Overview

```sh
npm install
```

```sh
npm run dev
```

#### Remove Boilerplate

App.jsx

```js
function App() {
  return (
    <>
      <h1>React App</h1>
    </>
  );
}

export default App;
```

#### Concurrently

```sh
npm i concurrently
```

[Concurrently](https://www.npmjs.com/package/concurrently)

package.json

```json
 "scripts": {
    "watch": "concurrently \"node --watch server.js \" \"cd client && npm run dev\"",
    "dev": "nodemon server.js",
    "watch-server": "node --watch server.js "
  },
```

#### Client Packages

!!! IMPORTANT !!!

```sh
cd client
```

```sh
npm i axios react-icons react-router-dom recharts styled-components dayjs react-toastify
```

#### Get Assets From Final

- get final project copy/paste assets directory

#### Title and Favicon

- change title in index.html
- replace favicon.ico in public

```html
<head>
  <link rel="icon" type="image/svg+xml" href="/favicon.ico" />
  <title>Jobify</title>
</head>
```

- resource [Generate Favicons](https://favicon.io/)

#### Global Styles

- CSS in JS (styled-components)
- saves times on the setup
- less lines of css
- speeds up the development
- if any questions about specific styles
- Coding Addict - [Default Starter Video](https://youtu.be/UDdyGNlQK5w)
- Repo - [Default Starter Repo](https://github.com/john-smilga/default-starter)

#### Setup Initial Pages and Render Landing

- create pages directory
- setup Landing, Register, Error and Dashboard components/pages
- in VITE we MUST use .jsx extension
- import Landing in App.jsx

#### Landing Page - Structure

```js
import main from '../assets/images/main.svg';
import logo from '../assets/images/logo.svg';

const Landing = () => {
  return (
    <>
      <nav>
        <img src={logo} alt='jobify' className='logo' />
      </nav>
      <div className='container page'>
        {/* info */}
        <div className='info'>
          <h1>
            job <span>tracking</span> app
          </h1>
          <p>
            I'm baby wayfarers hoodie next level taiyaki brooklyn cliche blue
            bottle single-origin coffee chia. Aesthetic post-ironic venmo,
            quinoa lo-fi tote bag adaptogen everyday carry meggings +1 brunch
            narwhal.
          </p>
          <button className='btn btn-hero'>Login/Register</button>
        </div>
        <img src={main} alt='job hunt' className='img main-img' />
      </div>
    </>
  );
};

export default Landing;
```

#### Styled Components

- CSS in JS
- Styled Components
- have logic and styles in component
- no name collisions
- apply javascript logic
- [Styled Components Docs](https://styled-components.com/)
- [Styled Components Course](https://www.udemy.com/course/styled-components-tutorial-and-project-course/?referralCode=9DABB172FCB2625B663F)

```sh
npm install styled-components
```

```js
import styled from 'styled-components';

const El = styled.el`
  // styles go here
`;
```

```js
import styled from 'styled-components';

const Landing = () => {
  return (
    <>
      <StyledButton className='btn btn-hero'>Login/Register</StyledButton>
    </>
  );
};

const StyledButton = styled.button`
  background: red;
`;
```

- no name collisions, since unique class
- vscode-styled-components extension
- colors and bugs

#### Style Entire React Component

```js
const Wrapper = styled.el``;

const Component = () => {
  return (
    <Wrapper>
      <h1> Component</h1>
    </Wrapper>
  );
};
```

- only responsible for styling
- wrappers folder in assets

Landing.jsx

```jsx
import StyledWrapper from '../assets/wrappers/LandingPage.js';

const Landing = () => {
  return <StyledWrapper>....</StyledWrapper>;
};

export default Landing;
```

#### Logo and Images

- logo built in Figma
- [Cool Images](https://undraw.co/)

#### Logo

- create <b>components</b> folder in src
- create Logo.jsx
- move import and image logic
- export as default
- utilize index.js

components/index.js

```js
// first approach

import Logo from './Logo';
import SomeOtherComponent from './SomeOtherComponent';

export { Logo, SomeOtherComponent };

// second approach
export { default as Logo } from './Logo';
export { default as SomeOtherComponent } from './SomeOtherComponent';
```

#### React Router

(React Router Docs)[https://reactrouter.com/en/main]

```sh
npm i react-router-dom
```

App.jsx

```js
import Landing from './pages/Landing';
import { createBrowserRouter, RouterProvider } from 'react-router-dom';
const router = createBrowserRouter([
  {
    path: '/',
    element: <h1>home page</h1>,
  },
  {
    path: '/about',
    element: (
      <div>
        <h1>about page</h1>
      </div>
    ),
  },
]);

function App() {
  return <RouterProvider router={router} />;
}

export default App;
```

#### Setup Pages

- create index.js in pages
- export all pages
- setup routes in App.jsx

App.jsx

```js
import { Landing, Error, Dashboard, Register } from './pages';
import { createBrowserRouter, RouterProvider } from 'react-router-dom';
const router = createBrowserRouter([
  {
    path: '/',
    element: <Dashboard />,
  },
  {
    path: 'landing',
    element: <Landing />,
  },
  {
    path: 'register',
    element: <Register />,
  },
]);

function App() {
  return <RouterProvider router={router} />;
}

export default App;
```

#### Link Component

```js
<nav>
  <Link to='/'>Dashboard</Link>
  <Link to='/register'>Register</Link>
  <Link to='/landing'>Home</Link>
</nav>
```

Dashboard.jsx

```js
import { Link } from 'react-router-dom';
const Dashboard = () => {
  return (
    <div>
      <h1>Dashboard Page</h1>

      <Link to='/landing'>Landing page</Link>
      <br />
      <Link to='/register'>Register page</Link>
    </div>
  );
};

export default Dashboard;
```

Landing.jsx

```js
import { Link } from 'react-router-dom';

const Landing = () => {
  return (
    <StyledWrapper>
      ...
      <Link to='/register' className='btn btn-hero'>
        Login/Register
      </Link>
      ...
    </StyledWrapper>
  );
};

export default Landing;
```

#### Error Page

App.jsx

```js
const router = createBrowserRouter([
  {
    path: '/',
    element: <Dashboard />,
    errorElement: <Error />,
  },
  .....
]);
```

Error.jsx

```jsx
import { Link } from 'react-router-dom';
import img from '../assets/images/not-found.svg';
import Wrapper from '../assets/wrappers/ErrorPage';

const Error = () => {
  return (
    <Wrapper>
      <div>
        <img src={img} alt='not found' />
        <h3>Ohh! page not found</h3>
        <p>We can't seem to find the page you're looking for</p>
        <Link to='/'>back home</Link>
      </div>
    </Wrapper>
  );
};

export default Error;
```

#### Register Page

```js
import { Logo } from '../components';
import Wrapper from '../assets/wrappers/RegisterPage';

const Register = () => {
  return (
    <Wrapper>
      <form className='form'>
        <Logo />
        <h3>Register</h3>
        {/* name input */}
        <div className='form-row'>
          <label htmlFor='name' className='form-label'>
            name
          </label>
          <input type='text' id='name' name='name' className='form-input' />
        </div>

        <button type='submit' className='btn btn-block'>
          submit
        </button>
      </form>
    </Wrapper>
  );
};
export default Register;
```

#### Form Row Component

FormRow.jsx

```js
const FormRow = ({ type, name, labelText }) => {
  return (
    <div className='form-row'>
      <label htmlFor={name} className='form-label'>
        {labelText || name}
      </label>
      <input type={type} id={name} name={name} className='form-input' />
    </div>
  );
};

export default FormRow;
```

Register.jsx

```js
import { Logo, FormRow } from '../components';
import Wrapper from '../assets/wrappers/RegisterPage';

const Register = () => {
  return (
    <Wrapper>
      <form className='form'>
        <Logo />
        <h4>Register</h4>

        <FormRow type='text' name='name' />
        <FormRow type='email' name='email' />
        <FormRow type='password' name='password' />
        <button type='submit' className='btn btn-block'>
          submit
        </button>
      </form>
    </Wrapper>
  );
};
export default Register;
```

#### Toggle Member

Register.js

```jsx
import { Logo, FormRow } from '../components';
import Wrapper from '../assets/wrappers/RegisterPage';
import { useState } from 'react';

const Register = () => {
  const [isMember, setIsMember] = useState(true);

  return (
    <Wrapper>
      <form className='form'>
        <Logo />
        <h4>{isMember ? 'Login' : 'Register'}</h4>
        {!isMember && <FormRow type='text' name='name' />}
        <FormRow type='email' name='email' />
        <FormRow type='password' name='password' />
        <button type='submit' className='btn btn-block'>
          submit
        </button>
        <p>
          {isMember ? 'Not a member yet?' : 'Already a member?'}
          <button
            type='button'
            onClick={() => setIsMember(!isMember)}
            className='member-btn'
          >
            {isMember ? 'Register' : 'Login'}
          </button>
        </p>
      </form>
    </Wrapper>
  );
};
export default Register;
```

#### FormData API

Register.jsx

```js
const handleSubmit = (e) => {
  e.preventDefault();
  const formData = new FormData(e.currentTarget);

  const values = [...formData.values()];
  const isEmpty = values.includes('');
  if (isEmpty) {
    console.log('empty values');
  }
  const data = Object.fromEntries(formData);
  console.log(data);
};
return (
  <Wrapper>
    <form className='form' onSubmit={handleSubmit}>
      ...
    </form>
  </Wrapper>
);
```

#### Custom Hook

- create utils/getFormValues

```js
const getFormValues = (form) => {
  const formData = new FormData(form);

  const values = [...formData.values()];
  const isEmpty = values.includes('');
  const data = Object.fromEntries(formData);
  return { isEmpty, data };
};

export default getFormValues;
```

Register.jsx

```js
import getFormValues from '../utils/getFormValues';
const Register = () => {
  const [isMember, setIsMember] = useState(true);

  const handleSubmit = (e) => {
    e.preventDefault();
    const { isEmpty, data } = getFormValues(e.currentTarget);

    if (isEmpty) {
      console.log('empty values');
      return;
    }
    console.log(data);
  };
  ....
};
```

#### React Toastify

[React Toastify](https://www.npmjs.com/package/react-toastify)

```sh
npm i react-toastify
```

main.jsx

```js
import 'react-toastify/dist/ReactToastify.css';
import { ToastContainer } from 'react-toastify';
import './index.css';

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
    <ToastContainer position='top-center' />
  </React.StrictMode>
);
```

Register.js

```js
import { toast } from 'react-toastify';

if (isEmpty) {
  toast.error('Please fill in all fields');
  return;
}
```

- modifications

position

<ToastContainer position='top-center' >

index.css

```css
.Toastify__toast {
  text-transform: capitalize;
}
```

#### CORS Error

server.js

```js
// test route
app.get('/api/v1/test', (req, res) => {
  res.json({ msg: 'api test route ' });
});
```

Register.jsx

```jsx
const fetchData = async () => {
  try {
    const response = await fetch('http://localhost:5100/api/v1/test');
    const data = await response.json();
    console.log(data);
  } catch (error) {
    console.log(error);
  }
};

useEffect(() => {
  fetchData();
}, []);
```

```js
try {
  const response = await fetch('/api/v1/test');
  const data = await response.json();
  console.log(data);
} catch (error) {
  console.log(error);
}
```

vite.config.js

```js
// https://vitejs.dev/config/
export default defineConfig({
  plugins: [react()],
  server: {
    proxy: {
      '/api': {
        target: 'http://localhost:5100/api',
        changeOrigin: true,
        rewrite: (path) => path.replace(/^\/api/, ''),
      },
    },
  },
});
```

#### Axios Intro and Get Request

#### Axios Post Request

```js
const handleSubmit = async (e) => {
  e.preventDefault();
  const { isEmpty, data } = getFormValues(e.currentTarget);

  if (isEmpty) {
    toast.error('Please fill in all fields');
    return;
  }
  try {
    const endpoint = isMember ? '/api/v1/auth/login' : '/api/v1/auth/register';
    const response = await axios.post(endpoint, data);
    const message = isMember ? 'Login successful' : 'Registration successful';
    toast.success(message);
  } catch (error) {
    toast.error(error.response.data.msg);
    console.log(error);
  }
};
```

#### Setup Global Context

- create contex.jsx

```js
import { useState, createContext, useContext } from 'react';

const AppContext = createContext();

export const AppProvider = ({ children }) => {
  const [user, setUser] = useState(null);

  return (
    <AppContext.Provider value={{ user, setUser }}>
      {children}
    </AppContext.Provider>
  );
};

export const useGlobalContext = () => useContext(AppContext);
```

main.jsx

```js
import { AppProvider } from './context.jsx';
ReactDOM.createRoot(document.getElementById('root')).render(
  <AppProvider>
    <App />
    <ToastContainer position='top-center' />
  </AppProvider>
);
```

#### Set User and Navigate to Dashboard

Register.jsx

```js
import { useNavigate } from 'react-router-dom';
import { useGlobalContext } from '../context';

const Register = () => {
  const [isMember, setIsMember] = useState(true);
  const { setUser } = useGlobalContext();
  const navigate = useNavigate();

  const handleSubmit = async (e) => {
     .....
      setUser(response.data);
      const message = isMember ? 'Login successful' : 'Registration successful';
      toast.success(message);
      navigate('/');
    .....
  };
};
```

#### Temp Register Page

Register

```js
import { Logo, FormRow } from '../components';
import Wrapper from '../assets/wrappers/RegisterPage';
import { useEffect, useState } from 'react';
import getFormValues from '../utils/getFormValues';
import { toast } from 'react-toastify';
import { useGlobalContext } from '../context';
import axios from 'axios';
import { useNavigate } from 'react-router-dom';

const Register = () => {
  const [isMember, setIsMember] = useState(true);
  const { setUser } = useGlobalContext();
  const navigate = useNavigate();
  const handleSubmit = async (e) => {
    e.preventDefault();
    const { isEmpty, data } = getFormValues(e.currentTarget);

    if (isEmpty) {
      toast.error('Please fill in all fields');
      return;
    }
    try {
      const endpoint = isMember
        ? '/api/v1/auth/login'
        : '/api/v1/auth/register';
      const response = await axios.post(endpoint, data);
      setUser(response.data);

      const message = isMember ? 'Login successful' : 'Registration successful';
      toast.success(message);
      navigate('/');
    } catch (error) {
      toast.error(error.response.data.msg);
      console.log(error);
    }
  };
  return (
    <Wrapper>
      <form className='form' onSubmit={handleSubmit}>
        <Logo />
        <h4>{isMember ? 'Login' : 'Register'}</h4>
        {!isMember && <FormRow type='text' name='name' />}
        <FormRow type='email' name='email' />
        <FormRow type='password' name='password' />
        <button type='submit' className='btn btn-block'>
          submit
        </button>
        <p>
          {isMember ? 'Not a member yet?' : 'Already a member?'}
          <button
            type='button'
            onClick={() => setIsMember(!isMember)}
            className='member-btn'
          >
            {isMember ? 'Register' : 'Login'}
          </button>
        </p>
      </form>
    </Wrapper>
  );
};
export default Register;
```

#### Dashboard Pages

- create AddJob, AllJobs, Profile, Stats in pages directory
- setup children

App.jsx

```js
import {
  Landing,
  Error,
  Dashboard,
  Register,
  Stats,
  AllJobs,
  AddJob,
  Profile,
} from './pages';
import { createBrowserRouter, RouterProvider } from 'react-router-dom';

const router = createBrowserRouter([
  {
    path: '/',
    element: <Dashboard />,
    errorElement: <Error />,
    children: [
      {
        path: 'stats',
        element: <Stats />,
      },
      {
        path: 'all-jobs',
        element: <AllJobs />,
      },
      {
        path: 'add-job',
        element: <AddJob />,
      },
      {
        path: 'profile',
        element: <Profile />,
      },
    ],
  },
]);
```

Dashboard.jsx

```js
import { Link, Outlet } from 'react-router-dom';
const Dashboard = () => {
  return (
    <div>
      <nav>navbar</nav>

      <Outlet />
    </div>
  );
};

export default Dashboard;
```

App.jsx

```js
children: [
  {
    index: true,
    element: <Stats />,
  },
];
```
